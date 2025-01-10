---
title: Megatron:从思想到实现
date: 2025-01-06 13:52:43
updated: 2025-01-10 14:04:49
tags:
    - MLsys
excerpt: paper&code
---
Megatron 是由 NVIDIA 开发的一个用于大规模训练深度学习模型的分布式框架，特别针对大语言模型（LLMs）进行了优化。

## 思想

### Model Parallel Transformers

出自《Megatron-LM: Training Multi-Billion Parameter Language Models Using Model Parallelism》，实际是张量并行。

#### 降低通信量的方法

我们从MLP层出发，表达式为：
$$
Y=GeLU(XA)
$$
把X按列切分，A按行切分：
$$
X=[X_1,X_2],A=[A_1,A_2]^T
$$
但是$GeLU$不是线性函数：
$$
Y=GeLU(X_1A_1+X_2A_2)\ne GeLU(X_1A_1)+GeLU(X_2A_2)
$$
这时，我们需要在$GeLU$层前添加一个同步点来同步$XA$的结果。

但也可以把A按列拆分，得到：
$$
Y=[Y_1,Y_2]=[GeLU(XA_1),GeLU(XA_2)]
$$
这样可以节省一次同步操作。

同时，输出嵌入时也可以采取一样的策略把同步数据量从$bsv$降低到$bs$($b$是bath-size,$s$是sequence length,$v$是vocabulary size).对于大模型中较大的$v$，这样做的效果是很好的。

![transformer张量并行](https://cdn.jsdelivr.net/gh/RedrockerLi/picx-images-hosting@master/MLsys/transformer张量并行.1e8oosklfk.webp)

上面是模型并行实现的流程图,$f,g$是all-reduce操作，在前向传递和反向传递中各需要2次。

#### 同步数据量

在MLP和Self-Attention中，一次通信的数据量是$bsh$（$h$是hidden dimension)

### pipline

出自《Efficient Large-Scale Language Model Training on GPU Clusters Using Megatron-LM》

在下面的分析中，会把流水线并行和硬件设计中的流水线对比。

#### pipline bubbles产生原因

在硬件设计中，无效的流水级产生原因通常是产生了数据相关，需要阻塞某个流水级。

在流水线并行中，pipline bubbles也可以看作是产生了数据相关——需要等待上一级前向传递完成或下一级反向传播完成。

![流水线并行](https://cdn.jsdelivr.net/gh/RedrockerLi/picx-images-hosting@master/MLsys/流水线并行.4ckyscqdu4.webp)

而且这种数据相关是必然会发生，无法规避的。而且每一个batch开始，结束都会产生填充，冲刷流水线带来的空泡。这个空泡的问题比较好解决，不断增大batch就好，如果一轮训练只有一次流水线，那就只有一次填充，冲刷流水线的开销。但这显然是不可能的。

所有可以曲线救国，把microbatch切得更小，每一级运行的时间更短，填充，冲刷流水线的开销就越小。当然，也不是切的越小越好，太小的矩阵运算反而会降低性能。

#### 如何拆分microbatch

我们先按照“反向传播可以进行就进行，前向传播用于填空”的思路来设计一下这个地方的流水线。

| 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    | 9    | 10   | 11   | 12   | 13   | 14   | 15   | 16   | 17   | 18   | 19   | 20   | 21   | 22   | 23   | 24   | 25   | 26   | 27   | 28   | 29   | 30   | 31   | 32   | 33   | 34   | 35   | 36   | 37   | 38   | 39   | 40   | 41   | 42   | 43   | 44   | 45   | 46   | 47   | 48   | 49   | 50   | 51   | 52   | 53   | 54   | 55   | 56   | 57   | 58   | 59   | 60   |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 1a   | 2a   | 3a   | 4a   | 1    | 2    | 3    | 4    | 5a   | 6a   | 7a   | 8a   |      | 1f   | 1f   | 2f   | 2f   | 3f   | 3f   | 4f   | 4f   | 5    | 6    | 7    | 8    | 1f   | 1f   | 2f   | 2f   | 3f   | 3f   | 4f   | 4f   |      |      |      |      |      |      |      | 5f   | 5f   | 6f   | 6f   | 7f   | 7f   | 8f   | 8f   |      |      |      |      | 5f   | 5f   | 6f   | 6f   | 7f   | 7f   | 8f   | 8f   |
|      | 1a   | 2a   | 3a   | 4a   | 1    | 2    | 3    | 4    | 5a   | 6a   | 1f   | 1f   | 2f   | 2f   | 3f   | 3f   | 4f   | 4f   | 7a   | 8a   |      | 5    | 1f   | 1f   | 2f   | 2f   | 3f   | 3f   | 4f   | 4f   | 6    | 7    | 8    |      |      |      |      | 5f   | 5f   | 6f   | 6f   | 7f   | 7f   | 8f   | 8f   |      |      |      |      | 5f   | 5f   | 6f   | 6f   | 7f   | 7f   | 8f   | 8f   |      |      |
|      |      | 1a   | 2a   | 3a   | 4a   | 1    | 2    | 3    | 1f   | 1f   | 2f   | 2f   | 3f   | 3f   | 4f   | 4f   | 4    |      |      |      | 1f   | 1f   | 2f   | 2f   | 3f   | 3f   | 4f   | 4f   | 5a   | 6a   | 7a   | 8a   | 5    | 6    | 7    | 5f   | 5f   | 6f   | 6f   | 7f   | 7f   | 8f   | 8f   | 8    |      |      |      | 5f   | 5f   | 6f   | 6f   | 7f   | 7f   | 8f   | 8f   |      |      |      |      |
|      |      |      | 1a   | 2a   | 3a   | 4a   | 1f   | 1f   | 2f   | 2f   | 3f   | 3f   | 4f   | 4f   | 1    | 2    | 3    | 4    | 1f   | 1f   | 2f   | 2f   | 3f   | 3f   | 4f   | 4f   | ！   | ！   | ！   | 5a   | 6a   | 7a   | 8a   | 5f   | 5f   | 6f   | 6f   | 7f   | 7f   | 8f   | 8f   | 5    | 6    | 7    | 8    | 5f   | 5f   | 6f   | 6f   | 7f   | 7f   | 8f   | 8f   |      |      |      |      |      |      |

有12段时间被浪费。下面分析性能损失的来源。

一定会被浪费的地方：流水线填充和冲刷。对于最后一个设备，填充和冲刷的时间浪费在一头一尾，中间没有浪费。但上面的构造在28-30的时间浪费在等待5a的前向传播。（每台设备计算时间一样，可以只分析一台）

所以真正的原则是：”不浪费除了流水线填充和冲刷的时间“。

下面是构造方法：最后一台设备可以随时发起反向传播，所以要让最后一台尽可能快速的收到更多前向传播的值。同时采取了交叉前，反向传播的方式减少了中间激活值的保存时间。

![流水线改进](https://cdn.jsdelivr.net/gh/RedrockerLi/picx-images-hosting@master/MLsys/流水线改进.58hg7tj2dm.webp)

#### 流水线效率

记计算时间为$t_{id}$,空泡时间为$t_{pb}$,microbatch数量为$m$,流水线级数为$p$，则对优化前的流水线有：
$$
\frac{t_{pb}}{t_{id}}=\frac{p-1}{m}
$$
把一个microbatch分成$v$份，则对优化后的流水线有：
$$
\frac{t_{pb}}{t_{id}}=\frac{1}{v} \frac{p-1}{m}
$$
论文中的描述是：

> If each device has $𝑣$ stages (or model chunks), then the forward and backward time for a microbatch for each stage or chunk will now be $𝑡_𝑓 /𝑣$ and $𝑡_𝑏 /𝑣$.

理论上来说，可以不把设备资源严格划分为$v$块，设备内存足够保存$v$次前向传递，一次反向传播，一份为通信预留，一份模型参数的空间就可以完成上面的流水线，但文章中并未讨论。

#### 同步数据量

两级之间的通信量为$bsh$。在中间部分，前向和反向交替进行时通信和计算可以同时进行。

![piplineOverlap](https://cdn.jsdelivr.net/gh/RedrockerLi/picx-images-hosting@master/MLsys/piplineOverlap.32i1n3nuwo.webp)

### sequence parallelism

出自《Reducing Activation Recomputation in Large Transformer Models》

#### Activations Memory Per Transformer Layer

每个 Transformer 层由一个注意力块和一个 MLP 块组成，它们通过两个层归一化连接。下面，我们推导存储每个这些元素的激活所需的内存：

- 查询（$W^Q$）、键（$W^K$）和值（$W^V$）矩阵乘法：我们只需要存储它们的共享输入，大小为 $2bsh$.
- QK^T 矩阵乘法：需要存储 Q 和 K，总大小为$4bsh$.
- Softmax：需要一个大小为 $2as^2b$ 的 Softmax 输出用于反向传播.($a$:number of attention heads)
- Softmax dropout：只需要一个大小为 $as^2b$的掩码
- 对值（V）的注意力：我们需要存储 dropout 输出（$2as^2b$）和V($2bsh$)
- 结果和$W^O$矩阵乘：需要保存其输出，大小为 $2bsh$字节
- dropout：需要保存mask矩阵，大小为 $bsh$字节
- MLP：两个线性层存储它们的输入，大小分别为 $2bsh$ 和 $8bsh$。GeLU 非线性层也需要其输入，大小为 $8bsh$，用于反向传播。最后，dropout 存储其掩码，大小为 $bsh$
- Layer norm：每个层归一化存储其输入，大小为 $2bsh$，总共需要 $4bsh$ 的存储空间

总空间为$bsh(34+5\frac{as}{h})$

使用张量并行后：$bsh(10+\frac{24}{t}+5\frac{as}{ht})$（$t$:tensor parallel size）

上式中的$10$来自：

- QKV矩阵乘共享的大小为$2bsh$的输入
- 自注意力dropout的$bsh$的mask矩阵
- 第一个线性层输入需要$2bsh$字节
- MLP dropout的$bsh$的mask矩阵
- Layer norm$4bsh$ 的存储空间

这些内存占用的原因是对transformer块来说，被切分到不同GPU运算时，上面的值都要复制多份，所以应该思考怎么把这些部分拆到不同的GPU上。

#### 拆分方法

同样以MLP层为例，拆分方法如下图：

![sequenceParallelis](https://cdn.jsdelivr.net/gh/RedrockerLi/picx-images-hosting@master/MLsys/sequenceParallelis.2vets7nwx4.webp)

MLP dropout，Layer norm是怎么节省空间的很好解释，按序列的维度划分。但两个输入似乎还是要复制多份。这里采用的策略是：不储存完整的Y拥有反向传播，在第i个张量并行设备上保存$Y_i^s$部分，在反向传播是执行额外的all-gather。同时把通信和计算对Y的梯度并行，减少延迟。

内存消耗变为$bsh(\frac{34}{t}+5\frac{as}{ht})$

同时，这种策略和张量并行比起来通信量也没有上升。张量并行需要all-reduce，开销和加上序列并行的reduce-scatter+all-gather相等。

### selective activation recomputation

如果每层放已经检查点，可以把内存消耗降低为$2bsh$，但需要计算两次前向传递，也有额外的通信开销，不考虑。

为了平衡储存和运算的开销，我们选择占有较大内存，计算量不大的$\frac{5as}{h}$部分，当模型很大时，它会超过前面的34。同时重计算的成本也较低。

理论计算量为：
$$
72Bslh^2(1+\frac{s}{6h}+\frac{V}{12lh})
$$
实际计算量:
$$
72Bslh^2(1+\frac{s}{3h}+\frac{V}{12lh})
$$


## 实现

代码版本是tag: NVLM-1.0

### 程序入口

#### 配置文件

在`examples/gpt3/train_gpt3_175b_distributed.sh`文件中展示了训练脚本的运行过程，比较重要的参数有：

```shell
GPUS_PER_NODE=8
MASTER_ADDR=localhost
MASTER_PORT=6000
NUM_NODES=1
NODE_RANK=0
```

最后使用torchrun来运行：

```shell
torchrun ${DISTRIBUTED_ARGS[@]} pretrain_gpt.py \ 
    ${GPT_MODEL_ARGS[@]} \
    ${TRAINING_ARGS[@]} \
    ${MODEL_PARALLEL_ARGS[@]} \
    ${DATA_ARGS[@]} \
    ${EVAL_AND_LOGGING_ARGS[@]}
```

同时还有一些参数配置在`examples/gpt3/gpt_config.yaml`，在`parse_args（）`函数中被读取，==但并未找到`args.yaml_cfg`是怎样配置的。==

#### pretrain_gpt.py

```python
if __name__ == "__main__":
    # Temporary for transition to core datasets
    train_valid_test_datasets_provider.is_distributed = True
    pretrain(
        train_valid_test_datasets_provider, # 用于划分训练集和验证集
        model_provider,                     # 生成模型
        ModelType.encoder_or_decoder,       # 正在被训练的类型,判断是否要针对性优化
        forward_step,                       # 前向传播函数
        args_defaults={'tokenizer_type': 'GPT2BPETokenizer'},
    )
```

### 3D并行

#### 初始化

pretrain中调用的第一个函数是`initialize_megatron`。

```python
initialize_megatron(
    extra_args_provider=extra_args_provider,  # None
    args_defaults=args_defaults,		      # {'tokenizer_type': 'GPT2BPETokenizer'}
    get_embedding_ranks=get_embedding_ranks,  # None
    get_position_embedding_ranks=get_position_embedding_ranks # None
)
```

d核心功能是`initialize_model_parallel`提供的。

```python
mpu.initialize_model_parallel(
    # 张量并行的大小
    args.tensor_model_parallel_size, 
    # 流水线并行的大小
    args.pipeline_model_parallel_size, 
    # 虚拟流水线并行的大小
    args.virtual_pipeline_model_parallel_size,
    # decoder的起始rank
    args.pipeline_model_parallel_split_rank,
    # 上下文并行（把输入拆成context_parallel_size组）
    context_parallel_size =args.context_parallel_size,
    hierarchical_context_parallel_sizes =args.hierarchical_context_parallel_sizes,
    # 每个专家并行组中用于混合专家并行的 GPU 数量
    expert_model_parallel_size =args.expert_model_parallel_size,
    # 在数据并行域中分布式优化器的副本数量
    num_distributed_optimizer_instances =args.num_distributed_optimizer_instances,
    # 用于分割专家的各个张量的 GPU 数量
    expert_tensor_parallel_size =args.expert_tensor_parallel_size,
    # 分布式操作的超时时间
    distributed_timeout_minutes =args.distributed_timeout_minutes,
    # NCCL通信器的配置文件路径
    nccl_communicator_config_path =args.nccl_communicator_config_path,
    # 指定并行策略的顺序
    order ='tp-cp-ep-dp-pp' if not args.use_tp_pp_dp_mapping else 'tp-pp-dp',
    # 专门用于编码器的张量并行大小(上面的用于decoder)
    encoder_tensor_model_parallel_size =args.encoder_tensor_model_parallel_size,
    # 专门用于编码器的流水线并行大小
    encoder_pipeline_model_parallel_size =args.encoder_pipeline_model_parallel_size,
    # TODO
    get_embedding_ranks =get_embedding_ranks,
    get_position_embedding_ranks =get_position_embedding_ranks,
)
```

首先创建`RankGenerator`对象，初始化并行模式的正确配置和顺序。

```python
encoder_rank_generator = RankGenerator(
    tp=encoder_tensor_model_parallel_size,
    ep=1,
    dp=data_parallel_size,
    pp=encoder_pipeline_model_parallel_size,
    cp=context_parallel_size,
    order=order,
    rank_offset=0,
)
 decoder_rank_generator = RankGenerator(
    tp=tensor_model_parallel_size,
    ep=1,
    dp=data_parallel_size,
    pp=pipeline_model_parallel_size,
    cp=context_parallel_size,
    order=order,
    rank_offset=encoder_world_size,
)
```

重要方法：

```python
def get_ranks(self, token):
    # token是一个字符串，比如“tp-dp”，返回的mask是一个和self.order一样大的布尔数组。token中的并行类型在self.order中出现，则对应位置的mask为true，反之为fa l se
    mask = self.get_mask(self.order, token)
    ranks = generate_masked_orthogonal_rank_groups(self.world_size, self.ordered_size, mask)
    if self.rank_offset > 0:
        for rank_group in ranks:
            for i in range(len(rank_group)):
                rank_group[i] += self.rank_offset
    return ranks
```

`generate_masked_orthogonal_rank_groups()`函数用于划分进程组。会根据并行方法的并行度和token中出现的并行方法来生成。下面使用数学语言来描述，代码实现和数学语言一一对应：

假设`self.ordered_size`是`[tp_size, pp_size, dp_size]`，`mask`是`[True, False , True]`。

```
masked_shape=[tp_size, dp_size]
unmasked_shape=[pp_size]
global_stride=[1,tp_size,tp_size*pp_size,tp_size*pp_size*dp_size]
masked_stride=[1,tp_size*pp_size]
unmasked_stride=[tp_size]
group_size=tp_size*dp_size
num_of_group=world_size//(group_size)
对每一个组（for group_index in range(num_of_group)）
    解方程decomposed_group_idx*unmasked_shape=group_index1
    对组中的每一个成员（ for rank_in_group in range(group_size)）
        解方程decomposed_rank_idx*masked_shape=rank_in_group
        在rank中添加decomposed_rank_idx*masked_stride+decomposed_group_idx*unmasked_stride
  	在ranks中添加rank
```

这样做可以保证两点：

- 所有进程的rank不重复，和3D并行中的位置一一对应，且不随mask的变化而变化。
- 可以按并行方式给进程分组，把一组放在一个子列表里面，子列表个数是unmasked的size乘，表示按unmasked来划分子列表，每个子列表内都是在masked的并行方法中处于同一位置的rank。

`generator_wrapper`函数：

```python
for ranks in generator_wrapper('dp'):
    # 创建两个使用不同通信后端的进程组
    group = torch.distributed.new_group(
        ranks, timeout=timeout, pg_options=get_nccl_options('dp', nccl_comm_cfgs)
    )
    group_gloo = torch.distributed.new_group(ranks, timeout=timeout, backend="gloo")
    if rank in ranks:
        _DATA_PARALLEL_GROUP = group
        _DATA_PARALLEL_GROUP_GLOO = group_gloo
        _DATA_PARALLEL_GLOBAL_RANKS = ranks
```

这里dp方法会返回多个子列表，每一个子列表rank处在dp的同一位置，会交换数据，所以加入同一个通信组。

如果输入参数是pp,则会返回encoder和decoder处在同一位置的组，且encoder可以循环使用。

如果输入参数是tp-pp,则会返回encoder和decoder处在同一位置的组，且encoder不可以循环使用，encoder和decoder必须一样长。

==TODO:为什么会这样设置通信组==

同时使用`get_model`函数分割模型，主要是按照流水级划分，同时会设置数据并行和张量并行的参数。。

#### 训练

重要的函数是`forward_backward_pipelining_with_interleaving`，分块实现流水线的填充，正常运行，冲刷。

流水线并行通信：`p2p_communication.send_forward_recv_forward`->`_communicate`

```python
if config.use_ring_exchange_p2p:
    def _ring_exchange_wrapper(**kwargs):
        torch.distributed.ring_exchange(**kwargs)  # 使用环交换进行通信
        return []

    p2p_func = _ring_exchange_wrapper 
elif config.batch_p2p_comm:
    assert wait_on_reqs  # 确保等待请求
    p2p_func = _batched_p2p_ops  
else:
    p2p_func = _p2p_ops  
```

`initialize_model_parallel`时在使用`generator_wrapper('pp')`后设置了`_PIPELINE_MODEL_PARALLEL_GROUP`，设置时为当前rank所在组。在这里通信的时候指定该组。

==TODO:数据并行和张量并行在训练过程中没找到是怎么实现的。==

通过一些蛛丝马迹，初步猜测数据并行是在划分数据集，提供训练数据时实现的;张量并行是在定义模型时实现的。

### selective activation recomputation

在`class CheckpointFunction(torch.autograd.Function):`中实现。

```python
with torch.no_grad():
    outputs = run_function(*args)
if distribute_saved_activations:
    ctx.input_0_shape = args[0].data.shape
    safely_set_viewless_tensor_data(
        args[0], split_tensor_into_1d_equal_chunks(args[0].data, new_buffer=True)
    )
ctx.save_for_backward(*args)
```

在定义attention模块时使用：

```python
hidden_states = tensor_parallel.checkpoint(
        custom_forward, False, query, key, value,
        attention_mask, rotary_pos_emb, attn_mask_type
    )
```

