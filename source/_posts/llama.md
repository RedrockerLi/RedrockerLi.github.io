---
title: llama.cpp代码解读——推理
date: 2025-7-21 10:05:43
updated: 2025-8-7 21:58:08
tags: GPU motivation llama.cpp
excerpt: llama.cpp是如何推理的。
---

本文将顺着参考文献[1]的思路阅读代码(tag: b5943)。

# 项目结构

重要文件夹如下：

```
.
├── common         # 跨项目共享的通用代码（日志、工具函数等）
├── examples       # 使用示例（main/server等可执行文件的调用案例）
├── ggml           # 核心计算库（张量操作、CPU/GPU后端实现）
├── gguf-py        # GGUF模型格式的Python解析工具
├── grammars       # 输出格式约束文件（GBNF语法，如JSON/代码生成）
├── include        # 公共头文件（C/C++接口声明）
├── pocs           # 概念验证代码（实验性功能原型）
├── src            # 核心C/C++源代码（模型加载/推理实现）
└── vendor         # 第三方依赖源码（通过git submodule引入）
```

# tensor

## 定义

除开[1]中提到的，`struct ggml_tensor`中还有部分变量：

```c
int32_t flags;						// 标志位 {loss,param,output,input}
struct ggml_tensor * view_src;		// 源张量（如果是视图）
size_t               view_offs;		// 在源数据中的字节偏移量
void * extra;						// 后端专用数据
char padding[8];
```

## operations

`llama.cpp/ggml/src/ggml.c`有以下功能：

- 堆栈回溯、异常终止处理、日志、计时
- 内存管理：对齐（SIMD），非对齐
- 数据类型转换、注册：fp16, fp32. bf16 单个 多个 SIMD加速
- operation注册、实现

## graph compute

以`tests/test-backend-ops.cpp`为例看计算图的生成与运用。

生成：

- `main-->test_backend-->make_test_cases_eval`，生成很多case
- `main-->test_backend-->test->eval`
  - `build_graph`：不同的case会生成不同的运算
  - `ggml_new_graph`：在`ctx`上给`ggml_cgraph`各字段分配空间，注意是对齐分配。 
  - `ggml_build_forward_expand`：把tensor加入计算图
  - `ggml_graph_add_node`：添加node

用计算图计算：

- `main-->test_backend-->test->eval`
  - `ggml_backend_compare_graph_backend`：对比两个不同backend的计算结果
    - `ggml_backend_graph_compute`：调用不同后端计算

```c
enum ggml_status ggml_backend_graph_compute(ggml_backend_t backend, struct ggml_cgraph * cgraph) {
    enum ggml_status err = ggml_backend_graph_compute_async(backend, cgraph);
    ggml_backend_synchronize(backend);
    return err;
}

enum ggml_status ggml_backend_graph_compute_async(ggml_backend_t backend, struct ggml_cgraph * cgraph) {
    return backend->iface.graph_compute(backend, cgraph);
}
```



# 参考文献

[1] [understanding how llm inference works with llama cpp](https://www.omrimallis.com/posts/understanding-how-llm-inference-works-with-llama-cpp/)

[2] [llama.cpp源码解析](https://mp.weixin.qq.com/s?__biz=MzA4MjY4NTk0NQ==&mid=2247519554&idx=1&sn=c619e9907fb515e88b6265cf7017b726&chksm=9f8337d4a8f4bec27c755148f904668a46c85322e6ba31bc6b0286887bbb30f8a24a49d2d918&mpshare=1&scene=23&srcid=1107jzAJDvx0GcmHJO92iIcA&sharer_shareinfo=3dc56c7e8fdbf855398ab67e34dfb5c7&sharer_shareinfo_first=3dc56c7e8fdbf855398ab67e34dfb5c7#rd)

> 此版本比较老，新版本有很多变化

 
