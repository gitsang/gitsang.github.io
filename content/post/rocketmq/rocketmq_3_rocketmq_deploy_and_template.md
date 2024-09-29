---
title: 'RocketMQ 部署及编程示例'
description: ''
lead: ''
date: 2020-05-27T10:58:12+08:00
lastmod: 2020-05-27T10:58:12+08:00
draft: false
images: []
menu:
  docs:
    parent: 'rocketmq'
weight: 100
toc: true
---

## 1. 前言

虽然 RocketMQ 提供了 C++ 语言的客户端，但仍需在 Java 环境下进行部署。

本文开发环境：

- CentOS Linux release 7.5.1804 (Core)
- OpenJDK version 1.8.0_252
- Apache Maven 3.0.5 (Red Hat 3.0.5-17)
- RocketMQ 4.7.0

Maven 和 JDK 的安装都很简单，只需要到官网下载对应 tar 包解压后添加到环境变量即可，因此不再赘述。本文 JDK 使用的是 OpenJDK。同时需要说明的是 OpenJDK 是免费开源的项目，而如果你使用 OracleJDK 则需要注意版权问题（当然个人开发可以免费试用）

## 2. RocketMQ 的安装及配置

如果你在安装过程中遇到问题，请参考[RocketMQ 官方文档](http://rocketmq.apache.org/docs/quick-start/)

### 2.1 RocketMQ 的安装

```sh
# 到以下网址获取 zip 包下载链接:
# http://rocketmq.apache.org/dowloading/releases/
wget https://archive.apache.org/dist/rocketmq/4.7.0/rocketmq-all-4.7.0-source-release.zip
unzip rocketmq-all-4.7.0-source-release.zip
cd rocketmq-all-4.7.0/
mvn -Prelease-all -DskipTests clean install -U
```

执行成功后 RocketMQ 会被安装在当前目录的 `distribution/target/rocketmq-4.7.0/rocketmq-4.7.0` 目录下

```
cd distribution/target/rocketmq-4.7.0/rocketmq-4.7.0
```

### 2.2 运行

RocketMQ 由 4 个部分组成，Producer，Consumer，Name Server，Broker

需要分别将其启动

默认情况下 Name Server 会启动在 9876 端口，同时 log 文件会被创建在家目录 `~` 下

#### Start Name Server

```
nohup sh bin/mqnamesrv &
tail -f ~/logs/rocketmqlogs/namesrv.log
```

#### Start Broker

```
nohup sh bin/mqbroker -n localhost:9876 &
tail -f ~/logs/rocketmqlogs/broker.log
```

#### Send & Receive Message

RocketMQ 提供了多种语言的 Producer 和 Consumer 接口，这里可以先按照官方文档使用 Java 版自带的测试工具进行验证。

```
> export NAMESRV_ADDR=localhost:9876
> sh bin/tools.sh org.apache.rocketmq.example.quickstart.Producer
SendResult [sendStatus=SEND_OK, msgId= ...

> sh bin/tools.sh org.apache.rocketmq.example.quickstart.Consumer
ConsumeMessageThread_%d Receive New Messages: [MessageExt...
```

如果 sendStatus 显示为 SEND_OK，Consumer 也能收到消息则说明部署成功了

#### Shutdown Servers

```
sh bin/mqshutdown broker
sh bin/mqshutdown namesrv
```

> 如果你使用 Java 进行编程则可以继续参考官方文档的示例进行示例的编写，其他语言则需要参考对应语言的编程示例

### 2.3 服务配置

如果你需要修改服务配置，如监听端口，服务端口，部署模式等，可以在安装目录的 conf 文件夹下进行修改，或根据其配置文件新建自己的配置，然后在运行时加上参数 `-c <conf-file>` 来加载你的配置。

日志相关的配置再 conf 文件夹下以 logback 开头的文件下。

如果需要修改运行参数，如分配内存大小等，则需要修改对应的启动脚本。Name Server 的启动脚本在 `bin/runserver.sh`，Broker 的启动脚本在 `bin/runbroker.sh`。

RocketMQ 支持的集群模式配置，运行配置，日志配置等在安装目录下均有对应的参考配置。此处暂不细究，待到集群部署部分再进行详述。如果需要也可以参考 [RocketMQ 开发指导之二——RocketMQ 部署](https://blog.csdn.net/liitdar/article/details/87928544) 文章的内容

## 3. 基于 Cpp-Client 编写 RocketMQ 的生产者和消费者

### 3.1 克隆项目

使用 Cpp 客户端需要先从 github 上克隆项目 https://github.com/apache/rocketmq-client-cpp

```
git clone https://github.com/apache/rocketmq-client-cpp.git
```

### 3.2 编译和安装 rocketmq-client-cpp

> 以下内容翻译自 git 仓库 README

在运行编译脚本 build.sh 之前，请确保已安装以下编译工具或库。

- 编译工具：
  - gcc-c++ 4.8.2：需要支持 C++ 11 的编译器
  - cmake 2.8.0：编译 jsoncpp 需要它
  - automake 1.11.1：编译 libevent 需要它
  - autoconf 2.65：编译 libevent 需要它
  - libtool 2.2.6：编译 libevent 需要它
- 库：
  - bzip2-devel 1.0.6：boost 需要它
  - zlib-devel

该 build.sh 脚本将自动下载并建立依赖库包括了 libevent，json 和 boost。它将库保存在 rocketmq-client-cpp 文件夹下，然后为 rocketmq-client 构建静态库和共享库。如果依赖库构建失败，则可以尝试使用源 libevent 2.0.22，jsoncpp 0.10.7，boost 1.58.0 来手动编译

如果您的主机无法通过互联网下载这三个库源文件，则可以将这三个库源文件（release-2.0.22-stable.zip 0.10.7.zip 和 boost_1_58_0.tar.gz）复制到 rocketmq-client-cpp 的根目录，然后 build.sh 将自动使用三个库源文件来构建 rocketmq-client-cpp

使用以下命令进行编译

```
sh build.sh
```

编译完成后的 librocketmq.a 和 librocketmq.so 都保存在 `rocketmq-client-cpp/bin` 中，如果需要编译新的文件可以参考以下示例：

```
g++ -o consumer_example consumer_example.cpp -I/path/to/rocketmq-root-dir/include -L/path/to/rocketmq-root-dir/bin -lrocketmq -lpthread -lz -ldl -lrt -std=c++11
```

### 3.3 C++ 示例代码

#### 3.3.1 Producer 示例代码

在 rocketmq-client-cpp 目录的 example 文件夹下就提供了 Producer 的示例代码。

Producer 的代码可以简单概括为

- create producer
  - set Name Server address
- start producer
- send message
  - create message
    - set message topic/tags/keys/body
  - call interface to send message
  - destroy message
- shut down producer
- destroy producer

```cpp
#include <stdio.h>
#include "CCommon.h"
#include "CMessage.h"
#include "CProducer.h"
#include "CSendResult.h"

void StartSendMessage(CProducer* producer) {
  int i = 0;
  char body[256];
  CMessage* msg = CreateMessage("T_TestTopic");
  SetMessageTags(msg, "Test_Tag");
  SetMessageKeys(msg, "Test_Keys");
  CSendResult result;
  for (i = 0; i < 3; i++) {
    memset(body, 0, sizeof(body));
    snprintf(body, sizeof(body), "new message body, index %d", i);
    SetMessageBody(msg, body);
    int status = SendMessageSync(producer, msg, &result);
    if (status == OK) {
      printf("send message[%d] result status:%d, msgId:%s\n", i, (int)result.sendStatus, result.msgId);
    } else {
      printf("send message[%d] failed !\n", i);
    }
    usleep(1000);
  }
  DestroyMessage(msg);
}

int main(int argc, char* argv[]) {
  CProducer* producer = CreateProducer("Group_producer");
  SetProducerNameServerAddress(producer, "127.0.0.1:9876");
  StartProducer(producer);
  printf("Producer initialized. \n");

  StartSendMessage(producer);

  ShutdownProducer(producer);
  DestroyProducer(producer);
  printf("Producer stopped !\n");

  return 0;
}
```

#### 3.3.2 PushConsumer 示例代码

example 提供的 PushConsumer 稍显复杂，以下提供一个简化版本的代码以供理解其基本逻辑。

```
#include <unistd.h>
#include <stdlib.h>
#include <stdio.h>
#include <string>
#include "CPushConsumer.h"
#include "CMessageExt.h"

int doConsumeMessage(struct CPushConsumer *consumer, CMessageExt *msgExt) {
    printf("Topic:%s, Body:%s\n", GetMessageTopic(msgExt), GetMessageBody(msgExt));
    return E_CONSUME_SUCCESS;
}

int main(int argc, char *argv[]) {
    CPushConsumer *consumer = CreatePushConsumer("Group_Consumer_Test");
    SetPushConsumerNameServerAddress(consumer, "127.0.0.1:9876");

    Subscribe(consumer, "T_TestTopic", "*");
    RegisterMessageCallback(consumer, doConsumeMessage);

    StartPushConsumer(consumer);

    for (int i = 0; i < 6; i++) {
        sleep(10);
    }

    ShutdownPushConsumer(consumer);
    DestroyPushConsumer(consumer);

    return 0;
}
```

#### 3.3.3 编译运行

按照之前的示例编译即可

```
g++ -o t_producer t_producer.cpp -I/root/package/rocketmq-client-cpp/include/ -L/root/package/rocketmq-client-cpp/bin -lrocketmq -lpthread -lz -ldl -lrt -std=c++11
g++ -o t_pushConsumer t_pushConsumer.cpp -I/root/package/rocketmq-client-cpp/include/ -L/root/package/rocketmq-client-cpp/bin -lrocketmq -lpthread -lz -ldl -lrt -std=c++11
```

运行

```
# 第一个窗口
./t_producer

# 另一个窗口
./t_pushConsumer
```

producer 返回 result status:0 则发送成功

condumer 能收到消息则说明消费成功

---

## 参考

https://blog.csdn.net/liitdar/article/details/87928544

https://github.com/apache/rocketmq-client-cpp

https://github.com/apache/rocketmq/tree/master/docs/cn

http://rocketmq.apache.org/docs/quick-start/
