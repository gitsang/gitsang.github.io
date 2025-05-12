---
title: Build SSH Tunnel
slug: build-ssh-tunnel
description: |-
date: "2020-03-14T14:53:28+08:00"
lastmod: "2025-05-12T10:44:44+08:00"
weight: 1
categories:
  - "linux"
tags:
  - "SSH reverse tunnel"
  - "network access"
  - "autossh"
  - "Linux"
  - "CentOS"
  - "GatewayPorts"
---

<!-- markdown-front-matter -->

## 1. 使用场景

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

### 1.1 建立简单的 ssh 反向隧道

1. 在 LocalNet 机器上执行以下命令建立隧道

```
[LocalUser@LocalNet] $ ssh -p 22 -qngfNTR 10022:localhost:22 OuterUser@50.100.50.100
```

或（以下参数似乎比较稳定）[^1]

```
[root@LocalNet] $ ssh -fN -R :55555:localhost:22 50.100.50.100
```

2. 在 OuterNet 上连接 LocalNet

```sh
[OuterUser@OuterNet] $ ssh -p 10022 LocalUser@127.0.0.1
```

### 1.2 维持隧道

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

### 1.3 监听 0.0.0.0

如果需要监听 0.0.0.0 需要在服务端，即公网机器上开启 GatewayPorts

在 /etc/ssh/sshd_config 中把 `GatewayPorts` 设为 yes

## 2. 参考

[^1]: [ssh 端口转发：ssh 隧道](https://www.zsythink.net/archives/2450)
