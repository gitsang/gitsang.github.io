---
title: "Redis 常见报错及解决方法"
description: ""
lead: ""
date: 2020-05-14T14:53:28+08:00
lastmod: 2020-05-14T14:53:28+08:00
draft: false
images: []
menu: 
  docs:
    parent: "redis"
weight: 100
toc: true
---

## 1. somaxconn 设置过小 

### 1.1 告警信息

```
    WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
```

### 1.2 原因分析

somaxconn 限制了接收新 TCP 连接侦听队列的大小。对于一个经常处理新连接的高负载 web服务环境来说，默认的128太小了，大多数环境这个值建议增加到 1024 或者更多

### 1.3 解决方法

#### 方法 1

``` 
    echo 2048 > /proc/sys/net/core/somaxconn
```

#### 方法 2

```
    echo "net.core.somaxconn = 2048" >> /etc/sysctl.conf
    sysctl -p
```

### 1.4 内核参数 somaxconn

详见 https://www.jianshu.com/p/f23150649c26


## 2. 持久化数据正在写入

### 2.1 报错信息

```
    ERROR: LOADING Redis is loading the dataset in memory
```

### 2.2 原因分析

redis 持久化的数据正在重写写入内存，需要等待数据写入完成后才可正常访问

### 2.3 解决方法

修改配置文件 redis.conf

```
　　maxmemory 5GB
　　maxmemory-policy allkeys-lru
　　appendonly no
```

重启 redis 并 rewrite

```
    ./redis_multi rewrite all
```

## 3. overcommit_memory 被设置为 0

### 3.1 告警信息

```
    WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
```

### 3.2 原因分析

vm.overcommit_memory 被设置为 0， Background save 可能因为低内存失败

1. vm.overcommit_memory = 0 表示内核将检查是否有足够的可用内存供应用进程使用；如果有足够的可用内存，内存申请允许；否则，内存申请失败，并把错误返回给应用进程。
2. vm.overcommit_memory = 1 表示内核允许分配所有的物理内存，而不管当前的内存状态如何。
3. vm.overcommit_memory = 2 表示内核允许分配超过所有物理内存和交换空间总和的内存

### 3.3 解决方法

根据提示

```
    echo "vm.overcommit_memory = 1" > /etc/sysctl.conf
reboot
```

或

```
    sysctl vm.overcommit_memory=1
```

或

```
    echo 1 > /proc/sys/vm/overcommit_memory
```

## 4. 透明大页导致的告警

### 4.1 告警信息

```
    WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled.
```

### 4.2 原因分析

当前使用的是透明大页，可能导致redis延迟和内存使用问题。

### 4.3 解决方法

```
    echo never > /sys/kernel/mm/transparent_hugepage/enabled
```

并将此语句添加到 /etc/rc.local


