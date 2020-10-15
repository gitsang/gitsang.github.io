---
title: "搭建ssh反向隧道"
date: 2020-05-14T14:53:28+08:00
categories:
- programing
- linux
tags:
- linux
- ssh
keywords:
- 搭建ssh反向隧道
thumbnailImage: images/mapel.jpg
thumbnailImagePosition: right
metaAlignment: center
coverImage: images/mapel.jpg
coverMeta: in
coverSize: partial
---
<!--more-->

# 搭建ssh反向隧道 

## 使用场景

| 机器代号 | 操作系统 | 机器位置     | IP            | 账户名    | ssh/sshd 端口 |
| -------- | -------- | ------------ | ------------- | --------- | ------------- |
| OuterNet | CentOS 7 | 公网         | 50.100.50.100 | OuterUser | 22            |
| LocalNet | CentOs 7 | 内网(局域网) | 10.200.100.10 | LocalUser | 22            |

想要通过公网上的机器 OuterNet 访问内网机器 LocalNet

并能够使用 OuterNet 机器的 10022 端口访问 LocalNet 机器的 22 端口

相当于

```sh
[OuterUser@OuterNet] $ ssh -p 10022 LocalUser@127.0.0.1
```

替代在内网时候

```sh
$ ssh LocalUser@10.200.100.10
```

### 建立简单的 ssh 反向隧道

1. 在 LocalNet 机器上执行以下命令建立隧道

```
[LocalUser@LocalNet] $ ssh -p 22 -qngfNTR 10022:localhost:22 OuterUser@50.100.50.100
```

或（以下参数似乎比较稳定）

```
[root@LocalNet] $ ssh -fN -R :55555:localhost:22 50.100.50.100
```

2. 在 OuterNet 上连接 LocalNet

```sh
[OuterUser@OuterNet] $ ssh -p 10022 LocalUser@127.0.0.1
```

### 维持隧道

1. 安装 autossh

```sh
[LocalUser@LocalNet] $ sudo yum install autossh
```

2. 内网建立隧道

```sh
[LocalUser@LocalNet] $ autossh -p 22 -M 7777 -NR 10022:localhost:22 OuterUser@50.100.50.100
```

3. 在 OuterNet 上连接 LocalNet

```sh
[OuterUser@OuterNet] $ ssh -p 10022 LocalUser@127.0.0.1
```





