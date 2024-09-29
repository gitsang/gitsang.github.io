---
title: "搭建ELK日志系统"
description: ""
lead: ""
date: 2020-05-16T22:29:30+08:00
lastmod: 2020-05-16T22:29:30+08:00
draft: false
images: []
menu: 
  docs:
    parent: "maintenance"
weight: 100
toc: true
---

<!--more-->


# 搭建ELK日志系统

## 1. elk 简介

[官网](https://www.elastic.co/cn/what-is/elk-stack)介绍:

“ELK”是三个开源项目的首字母缩写，这三个项目分别是：Elasticsearch、Logstash 和 Kibana。

Elasticsearch 是一个搜索和分析引擎。Logstash 是服务器端数据处理管道，能够同时从多个来源采集数据，转换数据，然后将数据发送到诸如 Elasticsearch 等“存储库”中。Kibana 则可以让用户在 Elasticsearch 中使用图形和图表对数据进行可视化。

Elastic Stack 是 ELK Stack 的更新换代产品。

## 2. 搭建过程简述

本次搭建我们采用 docker 安装 sebp/elk 的方式，一键部署 elk 系统，这比每个系统单独部署会省去许多不必要的麻烦。

这样的搭建方式我们仅仅需要安装运行 elk 和 filebeats 就可以完成了

搭建完成后通过 ip:5601 就可以访问到日志展示的页面了

## 3. 开始搭建

### 3.1 环境搭建

首先需要为你的系统安装 docker 环境，对于没接触过 docker 的同学，你可以将其简单理解为虚拟机（当然 docker 和虚拟机有本质的区别，有兴趣的可以自行搜索），docker 的安装很简单，对于 Linux / MacOS 几乎都只需要一条命令就可以安装完成了，而 Windows 则比较特殊，exe 安装包使用下一步安装法也很容易安装完成，但实际上 windows 平台某些版本是无法安装成功的，比如家庭版 windows，不过对于服务器，应该很少有人用 windows 搭建的吧。

当然官方也提供了十分简介友好的快速入门教程 https://www.docker.com/get-started 包括了安装、使用和一些进阶操作，即使是英语苦手，也能轻松地入门，十分推荐大家去看一眼。

这里就仅以 CentOS 7.8 为例进行安装

```
# 更新包管理工具
yum update
# 安装 docker
yum install docker
# 启动 docker
service docker start
```

到这步为止，docker 就算安装完成了

### 3.2 拉取 elk 镜像并运行

docker 拥有非常完善地 hub，你可以在 https://hub.docker.com/ 找到各类的镜像。

我们在其中搜索 `sebp/elk` 就可以找到我们想要的镜像了，同样，如果你想要对每个服务单独安装，你也可以搜索 `elasticsearch` `kibana` `logstash` 查看。

![202005281456-elk_dockerhub.png](https://raw.githubusercontent.com/gitsang/gallery/master/page/202005281456-elk_dockerhub.png)

在 Overview 标签中，我们复制 Docker Pull Command `docker pull sebp/elk` 到系统中运行，即可完成镜像的拉取（国内的服务器可能会有些慢），如果你想要安装其他版本的 elk，也可以点击 Tags 标签获取其他版本的拉取方式。

等待拉取结束后，运行

```
docker run --name elk -it -d \
        -p 5601:5601 -p 5044:5044 -p 9200:9200 -p 9300:9300 \
        sebp/elk
```

其中：

- --name 用于指定 docker 名称
- -d 表示在后台运行 (detach)
- -it 用于保持 STDIN 开启和开启终端登录 (是为了使能够进入容器进行操作且退出时容器不会自动关闭)
- -p 表示端口的映射关系，如 `-p 5601:5601` 就表示将 docker 的 5601 端口映射到主机的 5601 端口

第一次运行可以不加 `-d` 参数，这样做能够方便你看到出现了哪些错误。

等待一段时间后通过浏览器输入 `ip:5601` 如果能够登录到 kibana 界面，那么 elk 就算是搭建成功了（对于性能不太好的机器 kibana 的加载可能会比较慢，耐心等待即可）

**如果你出现 vm.max_map_count 不足的错误，可以参考[这篇文章](https://www.jianshu.com/p/04f4d7b4a1d3)增加配置**

### 3.3 安装 filebeats

filebeats 作为 Elastic Stack 的新成员，主要负责日志的收集工作。

你可以在 https://www.elastic.co/cn/beats/filebeat 找到 filebeats 对应各系统的安装包进行安装。

同样以 CentOS 为例，下载并解压 filebeat

```
wget https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.7.0-linux-x86_64.tar.gz
tar zxvf https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.7.0-linux-x86_64.tar.gz
```

然后修改以下 filebeat 的配置，让 filebeat 知道我们需要采集的是哪个日志文件，用 vim (或其他文本编辑器) 打开配置文件

```
cd filebeat-7.7.0-linux-x86_64
vim filebeat.yml
```

修改 `filebeat.inputs:` `output.**:` 下的几个参数

```
filebeat.inputs:
- type: log
    enable: true # 这个一定要设置为 true 否则不会应用下面的参数
    paths:
        - /path/to/your/logfile # 这里填写你的 log 文件路径

# elasticsearch 和 logstash 只能二者选其一，不使用的需要将其注释掉
# 这里先以直接发送到 elasticsearch 为例
# 因为如果使用 logstash 还需要进行额外的设置

output.elasticsearch:
    hosts: ["localhost:9200"]

#output.logstash:
    #hosts: ["localhost:5044"]
```

设置完成后保存退出，然后运行

```
./filebeat -e -c filebeat.yml -d "publish"
```

然后到 `ip:5601/app/infra#/logs` 下如果能够查看到你的日志文件就说明成功了

![202005281456-kibana_logpage.png](https://raw.githubusercontent.com/gitsang/gallery/master/page/202005281456-kibana_logpage.png)

就可以终止 filebeats 然后在后台运行

```
nohup ./filebeat -e -c filebeat.yml -d "publish" > filebeat.log 2>&1 &
```

## 4. 对于性能不足的服务器进行的优化

如果使用上述方式，会消耗大约 2G 的内存，对于一些小型服务器，可能不足以支撑（比如我的 1C2G）

这时候就需要对 elk docker 进行一些限制了

### 4.1 分配 swap

```
# 设置 swap 文件
dd if=/dev/zero of=/.swap bs=1048576 count=4096
# 创建(格式化) swap 
mkswap /.swap
# 启动 swap
swapon /.swap
# 查看 swap 使用情况
# free -h
# 设置开机自启
echo "/.swap swap swap defaults 0 0" >> /etc/fstab
```

### 4.2 docker run 参数调优

```
docker run --name elk -it -d \
        -p 5601:5601 -p 5044:5044 -p 9200:9200 -p 9300:9300 \
        -e ELASTICSEARCH_START=1 \
        -e KIBANA_START=1 \
        -e LOGSTASH_START=0 \
        -e ES_HEAP_SIZE="256m" \
        -m 512M --memory-swap=2048M \
        sebp/elk
```

由于 filebeats 将数据直接发送到了 elasticsearch 所以 logstash 就不需要运行了，可以通过这只环境变量 `LOGSTASH_START=0` 禁止其运行， `ES_HEAP_SIZE="256m"` 限制 elasticsearch 堆栈使用大小，`-m 512M --memory-swap=2048M` 从 docker 层面限制 elk 容器能够使用的最大物理内存和 swap

对于其他的参数及环境变量的配置可以通过 https://elk-docker.readthedocs.io/ 了解，文档中对各个参数的用途都有详细的说明

### 4.3 其他调优方式

如果你有多台服务器，你也可以将 elk 部署到其他的性能较好的服务器上，然后通过 filebeats 指定对应的 ip 向其发送信息。这样也能最大程度地减少服务器负担。特别是对于多个服务器都需要采集数据的情况下，在每一台机器都部署一个 elk 完全没有必要。

## 5. 单独拉取每个系统镜像

仅作参考，如果你需要单独部署每个服务（或者你就是不想使用 sebp/elk），你可以参考以下方法

其实就是单独拉取镜像然后部署，修改配置

```
docker pull elasticsearch:7.7.0

docker run -d --name elasticsearch \
        --restart=always \
        -p 9200:9200 -p 9300:9300 \
        -e ES_JAVA_OPTS="-Xms128m -Xmx128m" \
        -e "discovery.type=single-node" \
        -m 128M --memory-swap=256M \
        docker.io/elasticsearch:7.7.0
```

```
docker pull kibana:7.7.0

docker run -d --name kibana \
        --restart=always \
        -p 5601:5601 \
        -m 256M --memory-swap=512M \
        docker.io/kibana:7.7.0

docker exec -it kibana bash

vi config/kibana.yml

# 修改 elasticsearch.hosts

exit

docker restart kibana
```

---
## 参考

swap:
https://www.jianshu.com/p/36a81d5ec54e
https://blog.csdn.net/yabingshi_tech/article/details/77323617

docker 限制内存
https://www.hangge.com/blog/cache/detail_2413.html

elk curl
http://www.ruanyifeng.com/blog/2017/08/elasticsearch.html

mcbbs
https://www.mcbbs.net/thread-863367-1-1.html

vm.max_map_count 不足
https://www.jianshu.com/p/04f4d7b4a1d3

elk-docker 安装
https://wsgzao.github.io/post/elk/
