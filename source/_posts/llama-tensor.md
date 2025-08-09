---
title: llama.cpp代码解读——tensor
date: 2025-7-24 17:13:04
updated: 2025-8-7 21:58:08
tags: GPU motivation llama.cpp
excerpt: ggml, maybe
---

# 数据类型转换

## 宏屏蔽

```c
float ggml_fp16_to_fp32(ggml_fp16_t x) {
#define ggml_fp16_to_fp32 do_not_use__ggml_fp16_to_fp32__in_ggml
    return GGML_FP16_TO_FP32(x);
}
```

如果调用了`ggml_fp16_to_fp32`，会在链接阶段报错，这样设计的目的是禁止用户调用这个函数。

在ggml内部则会使用函数指针调用。

# tensor operations

搜索`GGML_OP_NAME`和`GGML_OP_SYMBOL`即可

## operation

```c
static struct ggml_tensor * ggml_dup_impl(
        struct ggml_context * ctx,
        struct ggml_tensor  * a,
        bool                  inplace) {
    struct ggml_tensor * result = inplace ? ggml_view_tensor(ctx, a) : ggml_dup_tensor(ctx, a);

    result->op     = GGML_OP_DUP;
    result->src[0] = a;

    return result;
}

struct ggml_tensor * ggml_dup(
        struct ggml_context * ctx,
        struct ggml_tensor  * a) {
    return ggml_dup_impl(ctx, a, false);
}

struct ggml_tensor * ggml_dup_inplace(
        struct ggml_context * ctx,
        struct ggml_tensor  * a) {
    return ggml_dup_impl(ctx, a, true);
}
```

除了传入上下文，源操作数，还传入了`inplace`信号，如果`inplace`为真，则不分配新内存，覆盖操作数，反之分配新内存（在`ctx`上）。

为了和后端实现对应，简单把操作分为3类：

- 基础操作：最基本的数学运算，比如四则运算，三角函数
- 复合操作：由基础操作复合而成的操作，比如卷积
- 原地操作：只需要修改张量的`ne,nb`信息，不需要修改`data`

## 基础操作

### ADD/ADD1

ADD是tensor+tensor, ADD1是tensor+scalar(b)

### MUL

```c
const int64_t ne[4] = { a->ne[1], b->ne[1], b->ne[2], b->ne[3] };
```

在ggml使用行主序的情况下，a->ne[1]为行数（每列有几个元素），b>ne[1]为原矩阵的列数（b被转置过）。所以输出是结果的转置。

### MUL_ID

用于专家模型，注意区分`n_expert`和`n_expert_used`。a是模型的权重，b是输入（每个token有多个输入向量,每个专家槽位一个）

### OUT_PROD

```c
// a is broadcastable to b for ne[2] and ne[3] -> use b->ne[2] and b->ne[3]
const int64_t ne[4] = { a->ne[0], b->ne[0], b->ne[2], b->ne[3] };
```

外积，`a->ne[1]==b->ne[1]`，用于多通道批量处理

## 复合操作

### CONV

使用img2col加速卷积

```c
struct ggml_tensor * result =
    ggml_mul_mat(ctx,
	ggml_reshape_2d(ctx, im2col, im2col->ne[0],  im2col->ne[3] * im2col->ne[2] * im2col->ne[1]), // [N, OH, OW, IC * KH * KW] => [N*OH*OW, IC * KH * KW]
	ggml_reshape_2d(ctx, a, (a->ne[0] * a->ne[1] * a->ne[2]),  a->ne[3]));// [OC，IC, KH, KW] => [OC, IC * KH * KW]

result = ggml_reshape_4d(ctx, result, im2col->ne[1], im2col->ne[2], im2col->ne[3], a->ne[3]); // [OC, N, OH, OW]
result = ggml_cont(ctx, ggml_permute(ctx, result, 0, 1, 3, 2)); // [N, OC, OH, OW]
```

上面注释中标出的形状，都是`ne`完全颠倒过来的结果（二维情况下可以理解为 [row,col]）。

## 原地操作

### PERMUTE

维度置换，和PyTorch中定义不同，参数是原始维度的**目标位置**。

```c
ne[axis0] = a->ne[0];
ne[axis1] = a->ne[1];
ne[axis2] = a->ne[2];
ne[axis3] = a->ne[3];

nb[axis0] = a->nb[0];
nb[axis1] = a->nb[1];
nb[axis2] = a->nb[2];
nb[axis3] = a->nb[3];
```

`nb`无需从头计算，原因是，`nb`随坐标置换后，算出在内存中的偏移量不变。

# graph compute

tensor operations都会记录在context中，使用它来生成计算图。

## 数据结构

```c
struct ggml_hash_set {
    size_t size;
    ggml_bitset_t * used;       // whether or not the keys are in use i.e. set
    struct ggml_tensor ** keys; // actual tensors in the set, keys[i] is only defined if ggml_bitset_get(used, i)
};

struct ggml_cgraph {
    int size;    // maximum number of nodes/leafs/grads/grad_accs
    int n_nodes; // number of nodes currently in use
    int n_leafs; // number of leafs currently in use

    struct ggml_tensor ** nodes;     // tensors with data that can change if the graph is evaluated
    struct ggml_tensor ** grads;     // the outputs of these tensors are the gradients of the nodes
    struct ggml_tensor ** grad_accs; // accumulators for node gradients
    struct ggml_tensor ** leafs;     // tensors with constant data
    int32_t             * use_counts;// number of uses of each tensor, indexed by hash table slot

    struct ggml_hash_set visited_hash_set;

    enum ggml_cgraph_eval_order order;
};
```

## ggml_build_forward_expand

会默认使用追加模式：

```c
ggml_build_forward_impl(cgraph, tensor, true);
if (!expand) {
    // TODO: this branch isn't accessible anymore, maybe move this to ggml_build_forward_expand
    ggml_graph_clear(cgraph);
}
```

### ggml_visit_parents

在`ggml_visit_parents`中，首先会在哈希表中找到一个空位或者该node对应的位置。如果不在hash表中，添加到哈希表；如果在，直接返回`node_hash_pos`。

然后遍历该node的父节点，递归调用`ggml_visit_parents`,并`cgraph->use_counts[src_hash_pos]++;`。

最后，对于叶子节点，或者不是可训练参数（如常数），记录在`leafs`中；其他的记录在`nodes`中。

`ggml_build_forward_expand`其实就是完成了把节点添加到计算图的工作，再加上一些log和检查。

## backend

```c
typedef struct ggml_backend * ggml_backend_t;

struct ggml_backend_i {
    const char * (*get_name)(ggml_backend_t backend);

    void (*free)(ggml_backend_t backend);

    // (optional) asynchronous tensor data access
    void (*set_tensor_async)(ggml_backend_t backend, struct ggml_tensor * tensor, const void * data, size_t offset, size_t size);
    void (*get_tensor_async)(ggml_backend_t backend, const struct ggml_tensor * tensor, void * data, size_t offset, size_t size);
    bool (*cpy_tensor_async)(ggml_backend_t backend_src, ggml_backend_t backend_dst, const struct ggml_tensor * src, struct ggml_tensor * dst);

    // (optional) complete all pending operations (required if the backend supports async operations)
    void (*synchronize)(ggml_backend_t backend);

    // (optional) graph plans (not used currently)
    // compute graph with a plan
    ggml_backend_graph_plan_t (*graph_plan_create) (ggml_backend_t backend, const struct ggml_cgraph * cgraph);
    void                      (*graph_plan_free)   (ggml_backend_t backend, ggml_backend_graph_plan_t plan);
    // update the plan with a new graph - this should be faster than creating a new plan when the graph has the same topology
    void                      (*graph_plan_update) (ggml_backend_t backend, ggml_backend_graph_plan_t plan, const struct ggml_cgraph * cgraph);
    // compute the graph with the plan
    enum ggml_status          (*graph_plan_compute)(ggml_backend_t backend, ggml_backend_graph_plan_t plan);

    // compute graph (always async if supported by the backend)
    enum ggml_status          (*graph_compute)     (ggml_backend_t backend, struct ggml_cgraph * cgraph);

    // (optional) event synchronization
    // record an event on this stream
    void (*event_record)(ggml_backend_t backend, ggml_backend_event_t event);
    // wait for an event on on a different stream
    void (*event_wait)  (ggml_backend_t backend, ggml_backend_event_t event);
};

struct ggml_backend {
    ggml_guid_t guid;
    struct ggml_backend_i iface;
    ggml_backend_dev_t device;
    void * context;
};
```

可以看到不同backend共用同一个接口，这有利于高层次的逻辑实现，下面以cuda为例，看看后端的初始化和计算。

### 初始化

在`/ggml/src/ggml-cuda/ggml-cuda.cu`中，定义了`ggml_backend_i`的全部函数指针，和初始化函数。

```c
static const ggml_backend_i ggml_backend_cuda_interface = {
    /* .get_name                = */ ggml_backend_cuda_get_name,
    /* .free                    = */ ggml_backend_cuda_free,
    /* .set_tensor_async        = */ ggml_backend_cuda_set_tensor_async,
    /* .get_tensor_async        = */ ggml_backend_cuda_get_tensor_async,
    /* .cpy_tensor_async        = */ ggml_backend_cuda_cpy_tensor_async,
    /* .synchronize             = */ ggml_backend_cuda_synchronize,
    /* .graph_plan_create       = */ NULL,
    /* .graph_plan_free         = */ NULL,
    /* .graph_plan_update       = */ NULL,
    /* .graph_plan_compute      = */ NULL,
    /* .graph_compute           = */ ggml_backend_cuda_graph_compute,
    /* .event_record            = */ ggml_backend_cuda_event_record,
    /* .event_wait              = */ ggml_backend_cuda_event_wait,
};

ggml_backend_t ggml_backend_cuda_init(int device) {
    if (device < 0 || device >= ggml_backend_cuda_get_device_count()) {
        GGML_LOG_ERROR("%s: invalid device %d\n", __func__, device);
        return nullptr;
    }

    ggml_backend_cuda_context * ctx = new ggml_backend_cuda_context(device);
    if (ctx == nullptr) {
        GGML_LOG_ERROR("%s: failed to allocate context\n", __func__);
        return nullptr;
    }

    ggml_backend_t cuda_backend = new ggml_backend {
        /* .guid      = */ ggml_backend_cuda_guid(),
        /* .interface = */ ggml_backend_cuda_interface,
        /* .device    = */ ggml_backend_reg_dev_get(ggml_backend_cuda_reg(), device),
        /* .context   = */ ctx,
    };

    return cuda_backend;
}
```

同时，对设备也有类似的统一接口：

> 分离backend和device：同一个backend可能用到多个设备，通过设备级抽象可以针对不同设备优化。

```c
static const ggml_backend_device_i ggml_backend_cuda_device_interface = {
    /* .get_name                = */ ggml_backend_cuda_device_get_name,
    /* .get_description         = */ ggml_backend_cuda_device_get_description,
    /* .get_memory              = */ ggml_backend_cuda_device_get_memory,
    /* .get_type                = */ ggml_backend_cuda_device_get_type,
    /* .get_props               = */ ggml_backend_cuda_device_get_props,
    /* .init_backend            = */ ggml_backend_cuda_device_init_backend, //ggml_backend_cuda_init在这里被调用
    /* .get_buffer_type         = */ ggml_backend_cuda_device_get_buffer_type,
    /* .get_host_buffer_type    = */ ggml_backend_cuda_device_get_host_buffer_type,
    /* .buffer_from_host_ptr    = */ NULL,
    /* .supports_op             = */ ggml_backend_cuda_device_supports_op,
    /* .supports_buft           = */ ggml_backend_cuda_device_supports_buft,
    /* .offload_op              = */ ggml_backend_cuda_device_offload_op,
    /* .event_new               = */ ggml_backend_cuda_device_event_new,
    /* .event_free              = */ ggml_backend_cuda_device_event_free,
    /* .event_synchronize       = */ ggml_backend_cuda_device_event_synchronize,
};
static const ggml_backend_reg_i ggml_backend_cuda_reg_interface = {
    /* .get_name          = */ ggml_backend_cuda_reg_get_name,
    /* .get_device_count  = */ ggml_backend_cuda_reg_get_device_count,
    /* .get_device        = */ ggml_backend_cuda_reg_get_device, //从ggml_backend_reg的ctx字段读取
    /* .get_proc_address  = */ ggml_backend_cuda_reg_get_proc_address,
};

ggml_backend_dev_t dev = new ggml_backend_device {
    /* .iface   = */ ggml_backend_cuda_device_interface,
    /* .reg     = */ &reg,
    /* .context = */ dev_ctx
};

ctx->devices.push_back(dev);
reg =new ggml_backend_reg {
    /* .api_version = */ GGML_BACKEND_API_VERSION,
    /* .iface       = */ ggml_backend_cuda_reg_interface,
    /* .context     = */ ctx
};
```

所以，调用`ggml_backend_cuda_device_init_backend`的方法是：

- 加载动态链接库： `ggml_backend_load_all`

- 然后获取device。这里会调用`get_reg`,在初次调用的时候，便会根据宏定义创建一系列后端

> 选择后端可以在编译时的指令中体现

- 然后调用`ggml_backend_t backend = ggml_backend_dev_init(dev, NULL);`

```c
ggml_backend_t ggml_backend_dev_init(ggml_backend_dev_t device, const char * params) {
    return device->iface.init_backend(device, params);//ggml_backend_cuda_device_interface
}
```

### 计算

```c
for (int i = 0; i < cgraph->n_nodes; i++){
	ggml_tensor * node = cgraph->nodes[i];
	//...
	bool ok = ggml_cuda_compute_forward(*cuda_ctx, node);//计算node的值
	//...
}
```

在这里，是按照cgraph->nodes的顺序遍历计算，这和`ggml_visit_parents`时添加node的顺序相关。计算任何node的源操作数，都在`leafs`中，或者`nodes`中index更小的地方。

对于每一个具体的算子，会使用模板实现共用优化+不同运算符。
