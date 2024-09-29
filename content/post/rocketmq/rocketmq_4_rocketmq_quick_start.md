---
title: 'RocketMQ 快速入门'
description: ''
lead: ''
date: 2020-05-14T14:53:28+08:00
lastmod: 2020-05-14T14:53:28+08:00
draft: false
images: []
menu:
  docs:
    parent: 'rocketmq'
weight: 100
toc: true
---

RocketMQ 的安装和快速入门可以参考官方文档：
http://rocketmq.apache.org/docs/quick-start/

主要步骤为

unzip -> mvn build -> Start Name Server -> Start Broker -> Send & Receive Message -> Shutdown Servers

## 1. build

根据官方文档解压并 build

```
 > unzip rocketmq-all-4.6.1-source-release.zip
 > cd rocketmq-all-4.6.1/
 > mvn -Prelease-all -DskipTests clean install -U
 > cd distribution/target/apache-rocketmq
```

如果安装其他版本的 RocketMQ 失败建议尝试使用官方最新版的进行安装部署

- 比如按照其他教程安装 rocketmq-all-4.2.0-bin-release 版本时出现 Build 错误，如果不是必须使用该版本可以使用官方最新版重新安装

```sh
[INFO] Error stacktraces are turned on.
[INFO] Scanning for projects...
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 0.109s
[INFO] Finished at: Tue Feb 25 10:35:49 CST 2020
[INFO] Final Memory: 10M/483M
[INFO] ------------------------------------------------------------------------
[WARNING] The requested profile "release-all" could not be activated because it does not exist.
[ERROR] The goal you specified requires a project to execute but there is no POM in this directory (/root/project/rocketmq-all-4.2.0-bin-release). Please verify you invoked Maven from the correct directory. -> [Help 1]
org.apache.maven.lifecycle.MissingProjectException: The goal you specified requires a project to execute but there is no POM in this directory (/root/project/rocketmq-all-4.2.0-bin-release). Please verify you invoked Maven from the correct directory.
        at org.apache.maven.lifecycle.internal.LifecycleStarter.execute(LifecycleStarter.java:89)
        at org.apache.maven.DefaultMaven.doExecute(DefaultMaven.java:320)
        at org.apache.maven.DefaultMaven.execute(DefaultMaven.java:156)
        at org.apache.maven.cli.MavenCli.execute(MavenCli.java:537)
        at org.apache.maven.cli.MavenCli.doMain(MavenCli.java:196)
        at org.apache.maven.cli.MavenCli.main(MavenCli.java:141)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.codehaus.plexus.classworlds.launcher.Launcher.launchEnhanced(Launcher.java:290)
        at org.codehaus.plexus.classworlds.launcher.Launcher.launch(Launcher.java:230)
        at org.codehaus.plexus.classworlds.launcher.Launcher.mainWithExitCode(Launcher.java:414)
        at org.codehaus.plexus.classworlds.launcher.Launcher.main(Launcher.java:357)
[ERROR]
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR]
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/MissingProjectException
```

- 如果 mvn 成功，应该会显式如下信息

```sh
[INFO] Reactor Summary:
[INFO]
[INFO] Apache RocketMQ 4.6.1 ............................. SUCCESS [27.086s]
[INFO] rocketmq-logging 4.6.1 ............................ SUCCESS [6.473s]
[INFO] rocketmq-remoting 4.6.1 ........................... SUCCESS [14.091s]
[INFO] rocketmq-common 4.6.1 ............................. SUCCESS [10.506s]
[INFO] rocketmq-client 4.6.1 ............................. SUCCESS [10.648s]
[INFO] rocketmq-store 4.6.1 .............................. SUCCESS [7.832s]
[INFO] rocketmq-srvutil 4.6.1 ............................ SUCCESS [0.474s]
[INFO] rocketmq-filter 4.6.1 ............................. SUCCESS [2.033s]
[INFO] rocketmq-acl 4.6.1 ................................ SUCCESS [3.983s]
[INFO] rocketmq-broker 4.6.1 ............................. SUCCESS [3.226s]
[INFO] rocketmq-tools 4.6.1 .............................. SUCCESS [1.945s]
[INFO] rocketmq-namesrv 4.6.1 ............................ SUCCESS [0.925s]
[INFO] rocketmq-logappender 4.6.1 ........................ SUCCESS [2.969s]
[INFO] rocketmq-openmessaging 4.6.1 ...................... SUCCESS [2.127s]
[INFO] rocketmq-example 4.6.1 ............................ SUCCESS [1.074s]
[INFO] rocketmq-test 4.6.1 ............................... SUCCESS [5.010s]
[INFO] rocketmq-distribution 4.6.1 ....................... SUCCESS [2:16.341s]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 3:57.044s
[INFO] Finished at: Tue Feb 25 10:44:18 CST 2020
[INFO] Final Memory: 71M/1632M
[INFO] ------------------------------------------------------------------------
```

## 2. Start

根据官方文档，需要进入 rocketmq 的 bin 目录下后台运行 mqnamesrv 和 mqbroker

但是上一步的 `cd distribution/target/apache-rocketmq` 可能由于目录结构变化，rocketmq 路径会有所不同

2020.02.25 时从官网下载的 4.6.1 版本具体路径应该为 `/path/to/rocketmq-all-4.6.1-source-release/distribution/target/rocketmq-4.6.1/rocketmq-4.6.1/` 根据不同版本可能也会有不同的路径

### 2.1 Start Name Server

```sh
> nohup sh bin/mqnamesrv &
> tail -f ~/logs/rocketmqlogs/namesrv.log
The Name Server boot success...
```

根据官方文档直接运行 `nohup sh bin/mqnamesrv &` 可能会在 nohup.out 或输出中出现如下错误

```sh
sh: /usr/local/rocketmq/bin/runserver.sh: No such file or directory
```

这明显是由于环境变量配置错误导致的，runserver.sh 应该在当前目录下的 bin 里面，即本文的 `/path/to/rocketmq-all-4.6.1-source-release/distribution/target/rocketmq-4.6.1/rocketmq-4.6.1/bin` 目录下

因此要为 RocketMQ 添加环境变量

```sh
vim /etc/profile
```

打开 profile 文件新增 ROCKETMQ_HOME 和 PATH 环境变量

```sh
# ROCKETMQ_HOME 填自己的 rocketmq 路径，本文配置如下
export ROCKETMQ_HOME=/root/project/rocketmq-all-4.6.1-source-release/distribution/target/rocketmq-4.6.1/rocketmq-4.6.1
export PATH=$ROCKETMQ_HOME/bin:$PATH
```

配置完成后使用 `source /etc/profile` 重新加载 profile 文件，然后再重新执行之前的步骤即可

运行成功后，打开当前目录下的 `nohup.out` 能看到如下信息：

```sh
Java HotSpot(TM) 64-Bit Server VM warning: Using the DefNew young collector with the CMS collector is deprecated and will likely be removed in a future release
Java HotSpot(TM) 64-Bit Server VM warning: UseCMSCompactAtFullCollection is deprecated and will likely be removed in a future release.
The Name Server boot success. serializeType=JSON
```

并且默认情况下会在 `~/logs/rocketmqlogs/` 目录下创建 `namesrv.log` 文件，根据官方文档 `tailf` 这个文件可以看到如下类似的信息：

```sh
2020-02-25 11:16:01 INFO main - tls.client.authServer = false
2020-02-25 11:16:01 INFO main - tls.client.trustCertPath = null
2020-02-25 11:16:01 INFO main - Using OpenSSL provider
2020-02-25 11:16:02 INFO main - SSLContext created for server
2020-02-25 11:16:02 INFO main - Try to start service thread:FileWatchService started:false lastThread:null
2020-02-25 11:16:02 INFO NettyEventExecutor - NettyEventExecutor service started
2020-02-25 11:16:02 INFO FileWatchService - FileWatchService service started
2020-02-25 11:16:02 INFO main - The Name Server boot success. serializeType=JSON
2020-02-25 11:17:02 INFO NSScheduledThread1 - --------------------------------------------------------
2020-02-25 11:17:02 INFO NSScheduledThread1 - configTable SIZE: 0
```

### 2.2 Start Broker

根据官方文档启动 Broker 服务

```sh
> nohup sh bin/mqbroker -n localhost:9876 &
> tail -f ~/logs/rocketmqlogs/broker.log
The broker[%s, 172.30.30.233:10911] boot success...
```

与启动 Name Server 相同，运行成功后在 `nohup.out` 中能看到如下信息

```sh
The broker[tb.novalocal, 172.17.0.1:10911] boot success. serializeType=JSON and name server is localhost:9876
```

broker 的启动相对较慢，可耐心等待一段时间，启动成功后 `tail -f ~/logs/rocketmqlogs/broker.log` 可以看到如下类似的信息

```sh
2020-02-25 11:28:22 INFO main - The broker[tb.novalocal, 172.17.0.1:10911] boot success. serializeType=JSON and name server is localhost:9876
2020-02-25 11:28:31 INFO BrokerControllerScheduledThread1 - dispatch behind commit log 0 bytes
2020-02-25 11:28:31 INFO BrokerControllerScheduledThread1 - Slave fall behind master: 0 bytes
2020-02-25 11:28:32 INFO brokerOutApi_thread_2 - register broker[0]to name server localhost:9876 OK
2020-02-25 11:29:02 INFO brokerOutApi_thread_3 - register broker[0]to name server localhost:9876 OK
2020-02-25 11:29:21 INFO TransactionalMessageCheckService - create new topic TopicConfig [topicName=RMQ_SYS_TRANS_HALF_TOPIC, readQueueNums=1, writeQueueNums=1, perm=RW-, topicFilterType=SINGLE_TAG, topicSysFlag=0, order=false]
2020-02-25 11:29:21 INFO brokerOutApi_thread_4 - register broker[0]to name server localhost:9876 OK
2020-02-25 11:29:31 INFO BrokerControllerScheduledThread1 - dispatch behind commit log 0 bytes
2020-02-25 11:29:31 INFO BrokerControllerScheduledThread1 - Slave fall behind master: 0 bytes
2020-02-25 11:29:32 INFO brokerOutApi_thread_1 - register broker[0]to name server localhost:9876 OK
```

## 3. Send & Receive Messages

根据官方文档说明，在收发信息之前，我们需要告诉 clients 配置的 name servers 地址

我们可以用官网推荐的配置环境变量来完成 name servers 地址的配置

```sh
> export NAMESRV_ADDR=localhost:9876
```

接着使用 `tools.sh` 进行消息的收发

```sh
> sh bin/tools.sh org.apache.rocketmq.example.quickstart.Producer
SendResult [sendStatus=SEND_OK, msgId= ...
> sh bin/tools.sh org.apache.rocketmq.example.quickstart.Consumer
ConsumeMessageThread_%d Receive New Messages: [MessageExt...
```

`sh bin/tools.sh org.apache.rocketmq.example.quickstart.Producer` 运行后出现大量

```
SendResult [sendStatus=SEND_OK, msgId= ...
```

类似的信息且没有报错说明成功

`sh bin/tools.sh org.apache.rocketmq.example.quickstart.Consumer` 运行后出现大量

```
11:42:05.200 [main] DEBUG i.n.u.i.l.InternalLoggerFactory - Using SLF4J as the default logging framework
Consumer Started.
ConsumeMessageThread_1 Receive New Messages:
```

类似的信息且没有报错说明成功

不同的版本和系统等可能输出信息会有所不同，但一般来说能收到消息就可以了，不放心可以对比一下收发的 msgId
