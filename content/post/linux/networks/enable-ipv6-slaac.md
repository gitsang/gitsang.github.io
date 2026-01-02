---
title: 开启 IPv6 自动配置
slug: enable-ipv6-slaac
description: This guide provides instructions on enabling IPv6 functionality through sysctl options and configuring network interfaces for automatic address assignment using SLAAC.
date: "2024-12-04T10:43:07+08:00"
lastmod: "2025-05-12T10:45:32+08:00"
weight: 1
categories:
  - "linux"
tags:
  - "IPv6"
  - "sysctl"
  - "network configuration"
  - "SLAAC"
  - "Linux"
---

<!-- markdown-front-matter -->

## 1. sysctl 配置

在某些情况下，IPv6 并不会自动配置，需要手动开启 sysctl 选项

编辑 `/etc/sysctl.conf` 文件，并在末尾添加以下内容，输入 `sysctl -p` 立即生效配置

此选项将开启 IPv6 功能，通常情况下，只需要配置此选项即可开启 IPv6

```
net.ipv6.conf.default.disable_ipv6 = 0
net.ipv6.conf.all.disable_ipv6 = 0
```

此选项将允许数据包在 Interface 之间转发，当你的网络接口是使用桥接时，通常需要开启此选项

```
net.ipv6.conf.default.forwarding = 1
net.ipv6.conf.all.forwarding = 1
```

此选项将允许接收路由器通告，如果上面两个选项配置后仍然无法获取 IPv6 地址，可以尝试开启以下选项

```
net.ipv6.conf.all.accept_ra = 1
net.ipv6.conf.default.accept_ra = 1
```

此选项用于开启 SLAAC (Stateless Address Autoconfiguration)，一般默认是开着的

```
net.ipv6.conf.all.autoconf = 1
net.ipv6.conf.default.autoconf = 1
```

## 2. 网络接口配置

编辑 `/etc/network/interfaces`

```
iface vmbr0 inet6 auto
```

当 inet6 配置为 `auto` 时将使用 SLAAC 自动配置
