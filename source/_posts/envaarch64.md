---
title: 某云服务商的aarch64机器配环境寄录
date: 2025-01-23 20:45:27
updated: 2025-01-23 20:45:27
tags:  环境
excerpt: 如题，寄了才录。
---
最近使用了一台aarch64的服务器，踩了不少坑。

# 罪魁祸首

```sh
Architecture:                    aarch64
CPU op-mode(s):                  64-bit
Byte Order:                      Little Endian
CPU(s):                          48
On-line CPU(s) list:             0-47
Thread(s) per core:              1
Core(s) per socket:              24
Socket(s):                       2
NUMA node(s):                    2
Vendor ID:                       HiSilicon
Model:                           0
Model name:                      Kunpeng-920
```

可以看到这是一台华子的机器，整数性能（单核）很差，所以解压缩有点慢。

# whl

机器上有预编译的whl包，比如`torch-2.1.0+cu121-cp310-linux_aarch64.whl`。这里的cu是cuda版本，cp是python版本。

# cutlass

在必须自己编译，还需要用到cutlass的情况下，请认真阅读README。

但因为还要解决版本问题，所以最好的解决方案是在clone需要安装的库时加上`--recursive`，把正确的submodules也克隆下来。

> 这台机器不能访问github，需要在自己的电脑上clone，再打包、scp到服务器上。
>
> 不要直接下载github上对应tag的压缩包，这样不会下载submodules。

# pybind11

在编译TransformerEngine的时候可能会遇到一些错误，即使注意了submodules和其他依赖。

解决方法在：https://github.com/pybind/pybind11/issues/4606

> ==机器永远是对的！==