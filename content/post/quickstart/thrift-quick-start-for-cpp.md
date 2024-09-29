---
title: 'Thrift C++ 快速入门'
description: ''
slug: thrift-quick-start-for-cpp
date: 2020-05-14T14:53:28+08:00
lastmod: 2020-05-14T14:53:28+08:00
weight: 1
categories:
  - C++
tags:
  - C++
  - thrift
---

## 1. thrift 基本使用

### 1.1 创建 thrift 文件

```thrift
# worker.thrift
namespace cpp freebird

service WorkerManager {
   void ping()
}
```

### 1.2 生成 cpp 代码

```
thrift -r --gen cpp worker.thrift
```

在当前目录创建 gen-cpp 目录，里面包含了所有生成的 C++ 代码。

> worker_constants.cpp
>
> worker_constants.h
> WorkerManager.cpp
> WorkerManager.h
> WorkerManager_server.skeleton.cpp
> worker_types.cpp
> worker_types.h

WorkerManager_server.skeleton.cpp 就是 C++ 服务端的 main 函数入口文件

### 1.3 编写 Client[^1]

```cpp
/* Client.cpp */

#include <iostream>

#include <thrift/protocol/TBinaryProtocol.h>
#include <thrift/transport/TSocket.h>
#include <thrift/transport/TTransportUtils.h>

#include "WorkerManager.h"

using namespace std;
using namespace apache::thrift;
using namespace apache::thrift::protocol;
using namespace apache::thrift::transport;

using namespace freebird;

int main() {
    boost::shared_ptr<TTransport> socket(new TSocket("localhost", 9090));
    boost::shared_ptr<TTransport> transport(new TBufferedTransport(socket));
    boost::shared_ptr<TProtocol> protocol(new TBinaryProtocol(transport));
    WorkerManagerClient client(protocol);

    try {
        transport->open();

        client.ping();
        cout << "ping()" << endl;
        transport->close();
    } catch (TException& tx) {
        cout << "ERROR: " << tx.what() << endl;
    }
}
```

### 1.4 编译

```sh
# make.sh

# server
g++ -g -Wall -I./ -I/usr/local/include/thrift WorkerManager.cpp worker_types.cpp worker_constants.cpp WorkerManager_server.skeleton.cpp -L/usr/local/lib/*.so -lthrift -std=c++11 -o server

# client
g++ -g -Wall -I./ -I/usr/local/include/thrift WorkerManager.cpp worker_types.cpp worker_constants.cpp Client.cpp -L/usr/local/lib/*.so -lthrift -std=c++11 -o client
```

### 1.5 运行

```sh
./server
```

```sh
./client
```

## 2. 错误汇总

### 2.1 stdcxx 和 boost 冲突

```
/tmp/ccX2bX8q.o: In function `main':
/root/workspace/thrift/test3/gen-cpp/WorkerManager_server.skeleton.cpp:41: undefined reference to `apache::thrift::server::TSimpleServer::TSimpleServer(boost::shared_ptr<apache::thrift::TProcessor> const&, boost::shared_ptr<apache::thrift::transport::TServerTransport> const&, boost::shared_ptr<apache::thrift::transport::TTransportFactory> const&, boost::shared_ptr<apache::thrift::protocol::TProtocolFactory> const&)'
collect2: error: ld returned 1 exit status
```

由于链接的 thrift 为 stdcxx 编译版，而代码中又使用了 boost （使用了 boost 编译版的 thrift 进行代码生成），因此报错。[^2]

编译时添加 `-std=c++11` 即可，或将代码中使用 boost 的代码改为 stdcxx 亦可，或使用 stdcxx 编译版的 thrift 重新生成代码。

## 参考

[^1]: [用 C++编写 thrift 第一个例子 - csfreebird](https://blog.csdn.net/csfreebird/article/details/50319035)
[^2]: [Ubuntu thrift 服务编译 未找到 TSimpleServer 的引用 - Templar101](https://ask.csdn.net/questions/691659#answer_1269976)
