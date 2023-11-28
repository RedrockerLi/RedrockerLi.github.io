---
title: GitHub使用寄录
date: 2023-11-28 00:51:39
tags:  Debug
excerpt: 如题，寄了才录。
---

~~抛开不当所得被没收的风险~~，用GitHub还遇到了不少Bug，甚至可能会困扰我很长时间。

# GitHub无法推送之Connection closed by UNKNOWN port 65535

在正常使用git的过程中，执行`git push`后报错如下：

```shell
E connect(5, AF=2 127.0.0.1:8080, 16): Connection refused
kex_exchange_identification: Connection closed by remote host
Connection closed by UNKNOWN port 65535
```

其中`Connection closed by UNKNOWN port 65535`是第一次看见，在中文互联网和外网转了一大圈，试了很多方法都没有成功。于是决定自己找问题。使用`ssh -v git@github.com`来查看ssh连接到github的详细信息：

````shell
OpenSSH_8.9p1 Ubuntu-3ubuntu0.4, OpenSSL 3.0.2 15 Mar 2022
debug1: Reading configuration data /home/redrocker/.ssh/config
debug1: /home/redrocker/.ssh/config line 2: Applying options for github.com
debug1: Reading configuration data /etc/ssh/ssh_config
debug1: /etc/ssh/ssh_config line 19: include /etc/ssh/ssh_config.d/*.conf matched no files
debug1: /etc/ssh/ssh_config line 21: Applying options for *
debug1: Executing proxy command: exec socat - PROXY:127.0.0.1:github.com:22,proxyport=8080
debug1: identity file /home/redrocker/.ssh/id_rsa type 0
debug1: identity file /home/redrocker/.ssh/id_rsa-cert type -1
debug1: Local version string SSH-2.0-OpenSSH_8.9p1 Ubuntu-3ubuntu0.4
2023/11/24 10:53:59 socat[27516] E connect(5, AF=2 127.0.0.1:8080, 16): Connection refused
kex_exchange_identification: Connection closed by remote host
Connection closed by UNKNOWN port 65535
````

可以看到第7行使用了一条`PROXY:127.0.0.1:github.com:22,proxyport=8080`的命令，这是我此前按CSDN上的方法设置的github代理，在`~/.ssh/config`文件中。如果我没有记错，当时也是无法推送，问题和网络相关，设置后确实从无法推送变成了可以推送。

但clash的Proxy Mode是灰色，因此注释掉相关规则，再尝试即可成功推送。

至于其中的原理，先挖一个坑，当下解决了燃眉之急就够了。