---
title: '关于 Rocketmq 事务消息和两阶段提交的理解'
description: ''
lead: '本文主要是对事务、事务消息、两阶段提交中概念的理解和解读，因此不会过多地解释具体地运行逻辑。如果想要了解，可以参考前人的文章'
date: 2020-09-17T15:54:19+08:00
lastmod: 2020-09-17T15:54:19+08:00
draft: false
images: []
menu:
  docs:
    parent: 'rocketmq'
weight: 100
toc: true
---

## 1. 事务及两阶段提交

### 1.1 事务的 HelloWorld 级理解

许多文档或文章中都会使用那个张三给李四转钱的例子来解释事务及其存在的必要。(即 A 账户-100，B 账户+100 这两件事，应该同时成功或失败，不能一半成功一半失败)

而要解决这个问题就引入了两阶段提交，第一阶段向 A 和 B 发送 prepare，让 A 和 B 准备好扣钱和加钱，即冻结这部分钱，然后当 A 和 B 都检查完毕没有问题并返回准备完成后，执行第二阶段，发送 commit 让 A 和 B 执行操作。

这看起来通俗易懂，但似乎很容易地就发现了，这么做似乎还是会出现一个成功一个失败的问题啊。没错，确实如此，比如 commit 万一其中一个没收到，所以还有很多工作要做。

但你需要知道的是，实际上，到此为止两阶段提交已经"结束"了。接下来的问题其实已经不是两阶段提交需要解决的了。也就是 **“两阶段提交能处理业务异常的失败，处理不了系统异常的失败”**

因此，接下来我们也不会探讨如何解决这些问题，但我们还是需要知道有哪些问题需要解决以及由谁解决。也就是我们需要什么样的系统才能满足两阶段提交。

### 1.2 两阶段提交的系统稳定性探索

很显然到目前为止，两阶段提交已经为我们解决了很多的问题，比如 A 账户余额不足，B 账户不存在等，这些业务逻辑上的错误我们已经能够避免了。但系统的网络并不总是通畅的，系统也不是永远能稳定运行的。比如网络波动，比如宕机都会影响到执行的结果。遗憾的是，两阶段提交并不能解决这些问题。

“好在”我们需要的“仅仅”是保证最终一致，那么如果发送 commit 失败，我就能够通过不断重试最终让 commit 成功，或者是所有机器都宕机了，那这些机器就要保证在重启后能够互相协商，并且准备阶段需要把状态落盘进行持久化等等，甚至你还要考虑要是连硬盘都坏了呢。

这可以考虑到很深很深，但意外总会发生，我们只能尽可能减小他发生错误的概率，不过这些都不是两阶段提交的内容了。而我们在实现事务消息的时候也只需要根据实际情况做出相对合理的应对就行了，比如对于普通的业务我只需要保证只要硬盘不出问题我的系统就是稳定的，就足够了。

(这里还暂时不引入协调者这个概念，因为协调者和客户端的行为十分相似，即使不引入，也能够支撑讨论，引入后反而增加理解难度)

### 1.3 关于超时的讨论

很显然，我们无法保证系统的完全稳定，即使我们已经做好了充足的应对，但仍然需要考虑到如果系统真的出现了故障时，应该做些什么，或者不能做些什么。

我们需要知道，整个两阶段提交中，实际上只有两个地方会产生阻塞，第一是 client 向 A/B 发送 prepare 后需要等待 A/B 的相应，第二个地方是 A/B 向客户端相应 prepare 后需要等待客户端的 commit。

###### Q1: Client 等待 A/B 返回 prepare 结果时，如果长时间得不到某一个的相应，是否能够发起 rollback ？

可以，这实际上是非常保守的做法，以牺牲耗时和数据为代价，保证了系统的一致，也许 A/B 都已经做好了准备，但 Client 已经做好了最坏的打算，于是终止了一切。

实际上你也可以让 Client 进行一些探测来确定 A/B 的状态，再做决定，或者简单点地多等几秒。

###### Q2: A/B 准备完成，等待 Client 发送 commit 时，如果长时间收不到 commit，是否能单方面地 rollback/commit 或向 Client 发送终止？

只有当对 prepare 的响应是失败时才可以 rollback，实际上如果已经失败，那他甚至无需等待 Client 的回复就能直接 rollback，因为 Client 没有任何的理由能够发送 commit，无论如何 Client 都会发送 rollback，因此最终结果是统一的。

但如果对 prepare 的相应是成功，这时候无法知道 Client 会返回什么，Client 的返回结果取决于另一方。解决方法就是向另一方求证，或者是发起一轮终止协议操作，或者，多等几秒。而最坏的结果就是，网络中断了，任何请求都无法发出，那么只能继续等待，直到收到相应，或终止协议，或者 Client 的 rollback

## RocketMQ 的事务消息

理解了事务之后，似乎事务消息就不难理解了，然而，看看 rocketmq 官方文档的这张图

![transaction-execute-flow](https://rocketmq.apache.org/assets/images/blog/transaction-execute-flow.png)

再看看他的描述

1. 生产者将半消息发送到 MQ Server。
2. 发送返回成功后，执行本地事务。
3. 根据本地事务的执行结果，将 commit 或 rollback 消息发送到 MQ Server。
4. 如果在本地事务执行过程中缺少 commit/rollback 消息或生产者处于等待状态，MQ Server 将向同一组中的每个生产者发送检查消息，以获取事务状态。
5. 生产者根据本地事务状态回复 commit/rollback 消息。
6. commit 的消息将传递给 consumer，但是 rollback 的消息将被 MQ Server 丢弃

Emmm, 好像懂了，但是和之前事务的描述好像哪里有些不一样？

是的，事务消息和事务确实有着许多的区别，不过我们换一种方式，认为事务消息保证的则是本地事务和发消息这两个操作的同时成功和失败，是不是就和事务保证两个操作的同时成功和失败对应上了呢。

需要注意的是，这里的保证的是本地事务和发消息这两个操作，而不是保证本地事务和消费消息，消费消息是否能成功那就是另外的问题了。这个误会消除后也许许多问题都有答案了。

那么我们可以完全不看这张图和描述，总结的来说，RocketMQ 的事务消息，实际上就是，客户端通过二阶段提交，来保证本地事务和发消息给 MQ Server 操作业务逻辑上同时成功或失败。而其中 MQ Server 需要通过将消息修改 Topic 存入队列，回查事务状态，等一系列操作来解决系统错误导致的问题，来保证最终一致。

也许这样仍然不能解决你的一些疑问，下面同样用 Q&A 的方式，也许就能理清其中的关系。

###### Q1: 如果把发送普通消息和本地执行逻辑放在一个事务里面，如果执行事务成功就发送普通消息，如果失败就回滚好像也是可以，那么这种事务消息相对其有什么优势或者好处呢？

优势是多一道最终一致的保障，我只要发送了 commit 那 MQ Server 就必须保证这条消息能够提交，发送了 rollback 那 MQ Server 就必须保证这条消息不会提交，为了实现这个要求，broker 需要将消息落盘，需要不能确定消息是否要提交的时候回查，需要阻塞住这个事务，就是为了最终这条消息一定能被提交或废弃（最终一致）

先执行本地事务，成功就发送，不成功就回滚，这只考虑了本地事务的成功失败，没有考虑发送消息的成功失败，那不如就来看看不使用事务消息要保证最终一致还需要做什么。

发送消息成功了，那很好什么都不需要做了。如果发消息失败了，你要考虑是为什么失败，比如超时，那就要查 MQ 这条消息到底进去了没有（回查），有的话你再当作成功，没有的话你要重发。

并且你要保证修改完数据库后如果生产者挂掉，重启后他还需要知道“曾经有一条消息，数据库已经改了，但是还没发出去”，你就需要持久化这条消息（落盘）

然后你还要保证你成功之前其他人不会去改这条数据，不然可能产生时序问题（阻塞）

最后你就会发现，你已经逆向实现了一套事务消息，那么，为什么要造轮子呢。（不过实际上，在没有事务消息功能的消息中间件中，这就是其中的一种实现事务的方式）

###### Q2: 如果 MQ Servier 已经收到消息了，回 ACK 超时了，发送方是不是会重试？重试的话消息不就重复了吗？

如第二问所说，当然不能重试，应该先处理超时，确认 Server 到底是为什么没回复，如果无法确定，则当作失败，向 Server 发送 rollback（或者是其他终止请求防止事务阻塞），这是个客户端需要实现的内容。

###### Q3: 发送方二次确认（Commit 或是 Rollback），MQ Server 收到则更新或回滚，这里消息不是入队列了嘛？还可以直接定位到消息然后更新消息的投递状态？如果还可以更新短信的状态，MQ 感觉就像数据库了。

消息确实入队了，但是 Topic 不对，会将 Topic 改为 **_TRANS_** 之类的，这个队列不可以被消费，如果要 Commit 则把 Topic 改回去，要 RollBack 就删掉。这里的消息是存到 CommitLog 里面的。且实际上，在其他不支持事务消息的 MQ 上，就是拿数据库来做状态存储介质的。

###### Q4: 消息回查,MQ 定时回查业务系统的数据库提交状态，感觉像底层系统调用上层系统了，MQ 要回调很多层业务系统，这样有点怪？

对的，所以需要消息发送方实现 listener 和定义回查（回调）函数，但不是回调很多业务层系统，只回调一个 listener 的 checkState，里面怎么实现是发送方的事情。

如果要客户端去查 MQ 实际上也是可以的，但你一个事务消息，总不能让客户端来保证数据的一致吧。

###### Q5: RocketMQ 的事务消息是否能够用来当作保证发送的多条消息的最终一致？

答案是可以，但不建议滥用。

虽然在表述中，似乎 RocketMQ 保证的是本地事务和发送消息的最终一致，但实际上这个行为是由客户端决定的，就是说你也可以无视本地事务，对多个消息的，发送多条 prepare，然后本地事务可以直接发送 UnknownState 等待 MQ Server 的回查，当所有的 prepare 消息都返回成功后，将状态改为 Commit，等回查时 MQ Server 检测到所有的消息都 commit 了，将他们放进消费队列，他们就同时成功了。当然，他们在后续业务上（即消费端上）是否还会一致就需要消费端来保证了。

关于消费端的问题再多扯两句，如果是一系列的操作，对于生产端，他已经认为发送成功了，因此，为保证最终一致，如果丢消息了要从 MQ Server 重新拉，如果执行失败了要不断重试。但如果重试了很久它真的过不去了怎么办呢？回滚操作吗，是不是那是不是在此之前还要给这一系列操作上锁？回滚步骤很多怎么办，这可不像是 MQ 的回滚直接废弃消息就好。回滚之后呢，生产端的数据怎么回滚呢？

要处理这些问题，你几乎又要实现一个新的事务，这就是一开始说的滥用事务。实际上，我认为如果你真的有这么强的业务需求，不如直接实现一个生产端到消费端的业务逻辑，如果非要用 MQ 那就只能改源码了。

## 参考

1. [正确理解二阶段提交（Two-Phase Commit）, CSDN, 萧萧冷](https://blog.csdn.net/lengxiao1993/article/details/88290514)
2. [分布式事务解决方案-RocketMQ 实现可靠消息最终一致性, 知乎专栏, 战猿](https://zhuanlan.zhihu.com/p/98592346)
3. [The Design Of Transactional Message, RocketMQ Doc](https://rocketmq.apache.org/rocketmq/the-design-of-transactional-message/)
4. [Transaction example, RocketMQ Doc](https://rocketmq.apache.org/docs/transaction-example/)
5. [消息队列漫谈：如何使用消息队列实现分布式事务？, 知乎专栏, 阿茂](https://zhuanlan.zhihu.com/p/101974130)
6. [RocketMQ 事务消息学习及刨坑过程, 掘金, 1 黄鹰](https://juejin.im/post/6844903969941110797)
7. [rocketmq 事务消息入门介绍, 掘金, 匠心零度](https://juejin.im/post/6844903648502218765)

## 其他

没有太多的时间进行校对，并且我也是在学习的阶段，可能有许多地方理解有错误或不妥之处，希望能帮忙指出，即使只是错别字或是语义的错误，对我来说也是莫大的帮助。
