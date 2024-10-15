---
title: 'Firewalld 白名单配置方法'
slug: firewalld-white-list-configuration
description: |-
  Firewalld 白名单配置方法
date: 2024-05-07T11:09:00+08:00
lastmod: 2024-05-07T11:09:00+08:00
weight: 1
categories:
  - docker
tags:
  - docker
  - firewalld
---

## 白名单配置方法

以仅信任来自 `10.60.22.0/24`, `10.60.23.0/24` ip 端的连接为例

### 1. 配置信任来源

```sh
# 添加 IP 地址范围到 "trusted" 的区域
firewall-cmd --permanent --zone=trusted --add-source=10.60.22.0/24
firewall-cmd --permanent --zone=trusted --add-source=10.60.23.0/24
```

### 2. 配置默认拒绝

```sh
# 将默认的防火墙区域设置为 "drop"
firewall-cmd --set-default-zone=drop
# 将网络接口 eth0 分配给 "drop" 区域
firewall-cmd --permanent --zone=drop --change-interface=eth0
```

### 3. Reload 防火墙

```sh
firewall-cmd --reload
firewall-cmd --get-active-zones
```

get-active-zones 应该会得到类似如下配置

```sh
drop
  interfaces: eth0
trusted
  sources: 10.60.22.0/24 10.60.23.0/24
```
