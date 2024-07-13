---
title: 在校园网环境下的ssh远程连接
date: 2023-11-24 13:03:34
tags: 折腾杂记
excerpt: 为出门不带电脑做出的努力。
---

# 下载openssh-server

- 使用`sudo apt-get install -y openssh-server`命令即可下载openssh-server。

> 如果报Unable to locate package openssh-server，则可能是软件源没有更新。
>
> 可以执行`sudo apt-get update `,如果不成功则继续执行`sudo apt-get upgrade` 

- 此时ssh默认打开，再使用`sudo service ssh status`检查ssh运行情况,会输出`Active: active (running) since Tue 2023-10-10 20:00:11 CST; 11s ago`(按q退出)

- Ubuntu 默认使用 [ufw 防火墙](https://linux265.com/news/3793.html)配置工具，如果你启用了防火墙，请确保防火墙打开了 SSH 端口`sudo ufw allow ssh`

# 使用ssh

- 禁用：`sudo service ssh stop`

- 启用：`sudo service ssh start`

- 重启：`sudo service ssh restart`

- 使用`ssh username@ip_address`命令即可在同一局域网下连接到对应计算机。

校园网~~通常~~（我的）是一个局域网，这一点比较方便，但还有一些问题。

> 可能会报错：`Failed to start ssh.service: Unit ssh.service is masked.`使用`sudo systrmctl unmask ssh`即可。

## 非静态IP

在校园网中，IP地址是变化的（至少我无法获得静态IP，也无法保证电脑永远不关机）。

因此，需要写一个脚本来远程告知当前的IP地址,在这里使用邮件的方式。

- 使用`sudo apt-get install mailutils`来下载命令行发邮件的工具，安装时选择默认选项即可。
- 编写一个文件`sendIpInEmail`。

```shell
ifconfig > ~/Documents/auto/IP.txt #把IP信息写入一个文件
mail -s 'NEW IP' example@qq.com < ~/Documents/auto/IP.txt #把文件信息通过邮件发送出去
```

> 注意IP.txt的路径，不改也可以，会自动创建。
>
> 修改`example@qq.com`为自己的邮箱。

- 在`sendIpInEmail`的文件夹下使用`chmod +x sendIpInEmail`为该文件添加运行权限。
- 使用`./sendIpInEmail`来运行脚本即可发送邮件。

内容大致如下

> lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
>         inet 127.0.0.1  netmask 255.0.0.0
>         inet6 ::1  prefixlen 128  scopeid 0x10<host>
>         loop  txqueuelen 1000  (Local Loopback)
>         RX packets 297212  bytes 134643447 (134.6 MB)
>         RX errors 0  dropped 0  overruns 0  frame 0
>         TX packets 297212  bytes 134643447 (134.6 MB)
>         TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
>
> wlp0s20f3: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
>         inet **这个地方是正确的IP** netmask 255.255.128.0  broadcast 10.204.127.255
>         inet6 fd7a:5341:1935:0:89a2:6fac:1e45:af98  prefixlen 64  scopeid 0x0<global>
>         inet6 fd7a:5341:1935:0:ee98:3ee9:f87c:6ab9  prefixlen 64  scopeid 0x0<global>
>         inet6 2400:dd01:103a:1017:14b4:6cdd:be2f:f8a7  prefixlen 64  scopeid 0x0<global>
>         inet6 2400:dd01:103a:1017:8f63:8e7:403:5d3c  prefixlen 64  scopeid 0x0<global>
>         inet6 fe80::8eae:b686:38bd:13aa  prefixlen 64  scopeid 0x20<link>
>         ether c4:75:ab:d3:d1:73  txqueuelen 1000  (Ethernet)
>         RX packets 2424714  bytes 878853503 (878.8 MB)
>         RX errors 0  dropped 0  overruns 0  frame 0
>         TX packets 308007  bytes 70057896 (70.0 MB)
>         TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

但还需要让发送自动进行,如果使用ubuntu就非常方便。

打开**StartupApplications**,如下图所示,点击**add**即可开机自启动。

![image-20231010224932555](https://cdn.jsdelivr.net/gh/RedrockerLi/RedrockerLi.github.io@main/Pics/startupApplication.3ix8lmg7tkn4.webp)

开机自启动时必须等待联网后才能发送IP地址，因此把之前的文件改为如下所示。

```shell
ping -c 1 114.114.114.114 > /dev/null 2>&1
while([ $? -ne 0 ])
do
ping -c 1 114.114.114.114 > /dev/null 2>&1
done

ifconfig > ~/Documents/auto/IP.txt #把IP信息写入一个文件
mail -s 'NEW IP' example@qq.com < ~/Documents/auto/IP.txt #把文件信息通过邮件发送出去
```

## Termius的使用

这是一个全平台的软件，免费版的功能已足够使用。具体用法可以STFW。

## 内网穿透

通过花生壳可解决

`phddns status`查看花生壳状态。
