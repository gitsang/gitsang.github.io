---
title: 'RocketMQ 核心概念及术语'
description: ''
lead: ''
date: 2020-05-26T09:33:46+08:00
lastmod: 2020-05-26T09:33:46+08:00
draft: false
images: []
menu:
  docs:
    parent: 'rocketmq'
weight: 100
toc: true
---

### 1. RocketMQ 概述

### 1.1 RocketMQ 是什么

RocketMQ 是阿里巴巴开源的分布式消息中间件，并于 2016 年捐赠给了 Apache 基金会。并且经历了双十一万亿级数据的洗礼，性能和可靠性都又足够的保障。

### 1.2 为什么使用 RocketMQ

1. 使用 Java 开发，还提供了不同语言 (C++/Go) 的 SDK，可以比较方便地从源码解决问题

2. RocketMQ 原生支持分布式

3. RocketMQ 能够保证严格地消息顺序

4. 堆积了亿级的消息后，依然保持写入低延迟

## 2. RocketMQ 核心概念简述

### 2.1 角色

#### 生产者（Producer）

发送消息的客户端角色，通常是产生数据的业务

#### 生产者组（Producer Group）

发送同类消息的 producer 组合标识，这些 producer 通常发送逻辑一致。

对于普通消息，仅用作标识，无特别用处。主要用处是再发送事务消息时，如果某个 producer 崩溃，到达超时时间后，broker 能根据 group 查找组内的其他 producer 以确定这条消息是 commit 还是 rollback。

#### 消费者（Consumer）

消费消息的客户端角色。通常是后台处理异步消费的系统。

消费者从 brokers 中拉取消息，并且将消息传递给业务系统。从用户应用的角度看，提供了两种类型的消费者：pullconsumer 和 pushconsumer

#### 消费者组（Consumer Group）

消费同类消息的 consumer 组合标识，这些 consumer 通常消费逻辑一致。在消息消费方面达到负载均衡和容错。

注： rocketmq 要求同一个 consumer group 的消费者必须要拥有相同的注册信息，即必须要听一样的 topic (并且 tag 也一样)。

### 2.2 概念术语

#### 消息（Message）

消息就是被传递的信息。消息必须要有一个主题。消息还可以有可选的标记及键值对。

#### 消息队列（Message Queue）

消息的物理管理单位，每个 topic，每个 broker 都会有若干个 queue

#### 主题（Topic）

主题用于标识一类消息，是消息的逻辑管理单元。

#### 标记（Tag）

类似于子主题，业务通过 topic + tag 订阅到想要的消息

### 2.3 服务组件

#### 代理人（Broker）

是处理消息存储，转发等工作的服务器

- broker 以 group 区分，每个 group 有一个 master 和若干 slave

- broker 向所有的 nameserver 结点建立长连接，注册 topic 信息

#### 名称服务器（Name Server）

用于向客户端提供路由信息，客户端依靠 Name Server 获取对应 topic 的路由信息，从而决定连接哪些 Broker。

#### Filter Server

RocketMQ 可以允许消费者上传一个 Java 类给 Filter Server 进行过滤。

### 2.4 消息模式（Message Model）

#### 集群模式（Clustering）

RocketMQ 有多种 Broker 集群部署模式，常见的包括：单 Master 模式、多 Master 模式、多 Master 多 Slave 模式（异步复制）、多 Master 多 Slave 模式（同步双写）等。

每种模式都有各自的有缺点:

- 单 Master：损耗小，部署简单，但是风险极大，一般不会使用

- 多 Master：单个 Broker 挂掉对整体服务没影响，但挂掉的 Master 中的消息在恢复前不可订阅，消息实时性受到影响

- 多 Master 多 Slave（异步复制）：主备异步复制，会有 ms 级别的延迟，Master 挂掉可能导致 ms 级别的数据丢失（未同步到 slave）

- 多 Master 多 Slave（同步双写）：主备同步复制，能保证主从数据最终一致，可用性高，但性能稍低

下图为多 Master 多 Slave 结构

![多Master多Slave.png](https://raw.githubusercontent.com/gitsang/gallery/master/page/20200526114226.png)

在 Clustering 模式下，同一个 Consumer Group 里的每个 Consumer 消费订阅消息的一部分内容，从而达到负载均衡的目的。

#### 广播模式（Broadcasting）

在 Broadcasting 模式下，同一个 Consumer Group 里的每个 Consumer 都消费订阅的全部消息。

### 2.5 消息顺序（Message Order）

当使用 DefaultMQPushConsumer 时，可以按顺序或同时使用消息。

#### 顺序（Orderly）

按顺序消费消息意味着是按照生产者发送的顺序进行消费。如果使用全局顺序场景，必须确保使用该 topic 的只有一个消息队列。

顺序消费还分为普通顺序和严格顺序，普通顺序在 Broker 宕机情况下会出现短暂的顺序不一致，严格顺序则牺牲系统可用性，集群中只要一个不可用会导致整个集群不可用。

注意：如果指定了是有序消费，那么最大并发量是消费者组订阅的消息队列的数量。

#### 并发（Concurrently）

如果指定了并发消费消息，最大的并发量是为每个使用者客户端指定的线程池的数量。

注意：消息的顺序在此模式下是不能保证的。

---

## 参考

https://juejin.im/post/5d89e0cef265da03d9257900

https://developer.51cto.com/art/201902/591997.htm

https://www.jianshu.com/p/3afd610a8f7d

https://blog.csdn.net/liitdar/article/details/87928598?utm_medium=distribute.pc_relevant.none-task-blog-baidujs-3

https://blog.csdn.net/hu_zhiting/article/details/99703877

https://zhuanlan.zhihu.com/p/25069846

http://jm.taobao.org/2017/01/12/rocketmq-quick-start-in-10-minutes/
