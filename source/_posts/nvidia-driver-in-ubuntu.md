---
title: Ubuntu下nvidia显卡安装驱动
date: 2024-08-08 11:59:56
updated: 2024-08-08 11:59:56
tags:
    - 环境
excerpt: 没那么复杂
---
网络上的很多教程都太过于复杂，但事实上是有简单的方法的。

`ubuntu-drivers devices`会有推荐的驱动，再使用apt安装即可。

安装过程中会要求先设置一个密码，然后重启。

重启中选择`Enroll MDK `,然后输入之前设置的密码。开机后使用`nvidia-smi`检查。