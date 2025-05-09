---
title: Docker 端口映射防火墙规则配置
slug: firewall-rule-for-docker-port-mapping
description: Guidance on configuring Docker-specific firewall rules using firewalld and iptables, including stopping Docker, creating custom chains, and setting default rules.
date: "2024-05-07T11:09:00+08:00"
lastmod: "2025-05-09T18:41:41+08:00"
weight: 1
categories: 
- "Networking"
- "Docker"
- "Security"
tags: 
- "Docker"
- "firewalld"
- "iptables"
- "firewall rules"
- "network security"

<!-- markdown-front-matter auto -->
---

## 1. 背景

当 docker 使用端口映射时， docker daemon 会创建 DOCKER 链绕过 firewalld 建立 iptables 规则，可能使 firewall 规则失效。

可以通过修改 DOCKER-USER 链来管理 docker 的防火墙规则或禁用 firewalld 直接配置 iptables（不推荐）

### 1.1 停止 docker

**不要在 Docker 运行时 Reload firewalld，否则会导致 Docker 链被删除**

```sh
systemctl stop docker
```

### 1.2 清除并重建自定义规则链

```sh
firewall-cmd --permanent --direct --remove-chain ipv4 filter DOCKER-USER
firewall-cmd --permanent --direct --remove-rules ipv4 filter DOCKER-USER
firewall-cmd --permanent --direct --add-chain ipv4 filter DOCKER-USER
```

### 1.3 允许 Docker 容器出站流量返回

使用 `conntrack` 模块匹配 `RELATED`, `ESTABLISHED` 两种状态的连接

```sh
# 允许出站流量返回，因为建立连接 ESTABLISHED 的数据包已经通过了防火墙的出站规则。
# 此规则优先级为 1
# 没有完全理解这个逻辑，但是加了这条容器内就可以联网了
firewall-cmd --permanent --direct --add-rule ipv4 filter DOCKER-USER 1 \
  -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT \
  -m comment --comment 'Allow containers to connect to the outside world'
```

### 1.4 配置白名单

```sh
# 允许来自 IP 段的所有流量
firewall-cmd --permanent --direct --add-rule ipv4 filter DOCKER-USER 1 \
  -s 10.60.22.0/24 -j ACCEPT \
  -m comment --comment 'Allow IP 10.60.22.0/24 to access'
```

### 1.5 配置默认阻止

```sh
# 阻止其他的流量
firewall-cmd --permanent --direct --add-rule ipv4 filter DOCKER-USER 10 \
  -j REJECT -m comment --comment 'reject all other traffic to DOCKER-USER'
```

### 1.6 Reload 防火墙

```sh
firewall-cmd --reload
firewall-cmd --get-active-zones
```

### 1.7 重启 docker

```sh
systemctl start docker
```
