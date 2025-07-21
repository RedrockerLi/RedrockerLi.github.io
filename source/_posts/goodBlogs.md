---
title: 记录好文章
date: 2024-09-11 12:06:00
updated: 2024-09-11 12:06:00
tags: 归档
excerpt: 互联网应该有记忆。
---
# fctix5配置
[Debian的衍生发行版MXlinux 基础设置 更换国内源 中文输入法 wps 截图软件flameshot 虚拟机增强工具 共享文件夹](https://zhuanlan.zhihu.com/p/680178763)

精华截图：
``` shell
sudo apt purge fcitx* ibus* 
sudo apt install fcitx5 fcitx5-chinese-addons fcitx5-config-qt im-config fcitx5-frontend-gtk3 fcitx5-frontend-gtk4 fcitx5-frontend-qt5  kde-config-fcitx5


sudo vi /etc/profile
#在打开的文件中添加以下内容：
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS=@im=fcitx
#保存并关闭文件
```

![](https://cdn.jsdelivr.net/gh/RedrockerLi/RedrockerLi.github.io@main/Pics/fcitx5.788dhkttn700.webp)

# v2rayA安装与配置
[2024 最新 v2rayA 安装与配置教程 for Windows、Linux、Macos、Android](https://pengtech.net/network/v2rayA_install.html)

搜索关键词：梯子，翻墙。

教程类，一步一步跟着走就行。

``` shell
# --now 参数表示设置为开机启动并立即启动v2raya
sudo systemctl enable --now v2raya
# 查看服务状态
systemctl status v2raya
```
下面是可以使用github和gpt的配置。

![](https://cdn.jsdelivr.net/gh/RedrockerLi/RedrockerLi.github.io@main/Pics/v2rayA.30xcgd83hsc0.webp)
![](https://cdn.jsdelivr.net/gh/RedrockerLi/RedrockerLi.github.io@main/Pics/v2rayA.268vialv4ygw.webp)
