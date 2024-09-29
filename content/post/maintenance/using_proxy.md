---
title: "如何使用代理"
description: ""
lead: ""
date: 2021-01-21T12:43:30+08:00
lastmod: 2021-01-21T12:43:30+08:00
draft: false
images: []
menu: 
  docs:
    parent: "maintenance"
weight: 100
toc: true
---

## 1. 背景

### 1.1 我们什么时候需要使用代理

假设你在家里或公司拥有一个服务器，或一个服务器集群，但这些服务器都是仅在内网开放的，此时你想要在家或公司之外访问内部的网络，有两种方法，一种是将内网的服务开放到公网，第二种就是使用代理。

这里说的代理是泛指，大致包括代理服务器，跳板机，远程连接等。他们最基本的原理就是，使用一个能同时连接公网和内网的机器作为中介，这台机器可以是物理机或者虚拟机，可以是服务器集群内的某一台机器，或者是服务器的某个端口。这个中介同时连接公网和内网，将公网的请求转发到内网，将内网的响应转发到公网。

通俗的解释就是，你想要拿到某人家中的某物，但你自己进不去房子，于是你贿赂他们家的小孩让他帮你拿出来，这个小孩就是中介（或者叫代理）。

## 2. 使用 v2ray

### 2.1 Windows 系统

#### 2.1.2 V2rayN

Windows 下有许多 v2ray 的 ui 界面，比较出名的是 v2rayN，下面仅以此软件作为说明，其他软件大同小异。

##### 2.1.2.1 下载 v2rayN

如果是第一次使用 v2ray，你需要从 github 上下载一个 [v2rayN-core](https://github.com/2dust/v2rayN/releases/download/3.29/v2rayN-Core.zip)，如果下载较慢也可以从[此处](http://h5ai.home.frp-sh.sang.pp.ua:8080/package/v2ray/v2rayN-Core.zip)下载

如果报毒需要先关闭相关杀毒软件，或白名单放行。

##### 2.1.2.2 配置 v2rayN

1. 下载并解压软件后，进入文件夹内，右键 `v2rayN.exe` 以管理员身份运行。

2. 点击 `服务器` -> `添加服务器` 以添加服务器配置，或在页面上直接 `ctrl+v` 从剪贴板导入

![v2rayN-01](http://h5ai.home.frp-sh.sang.pp.ua:8080/project/gallery/page/20210121-221938-v2rayN-01.png)

3. 右击任务栏右下角 V2RayN 图标 -> `服务器` -> 选择刚才添加的线路

![v2rayN-02](http://h5ai.home.frp-sh.sang.pp.ua:8080/project/gallery/page/20210121-222523-v2rayN-02.png)

4. 点击 `设置` -> `参数设置`

![v2rayN-03](http://h5ai.home.frp-sh.sang.pp.ua:8080/project/gallery/page/20210121-222631-v2rayN-03.png)

5. 在 `基础设置` 中配置监听端口，一般不需要修改

![v2rayN-04](http://h5ai.home.frp-sh.sang.pp.ua:8080/project/gallery/page/20210121-222725-v2rayN-04.png)

6. 在 `v2rayN设置` 中勾选 `允许来自局域网的连接`。这个选项会将 v2ray 的 http/socks 端口暴露，**请一定只在局域网内勾选此选项**，选项勾选后，与该机器连接在同一个网段上（连接同一个wifi的）的其他机器就可以使用这台机器的代理了

![v2rayN-05](http://h5ai.home.frp-sh.sang.pp.ua:8080/project/gallery/page/20210121-222809-v2rayN-05.png)

7. 全部配置完成后，点击 `重启服务`，看到日志输出类似 `V2Ray x.x.x started` 就说明启动成功了，这时候可以在页面的下方看到 socks5 和 http 的两个地址，这两个地址就是本地代理的地址，你可以直接将其配置在浏览器上。

![v2rayN-07](http://h5ai.home.frp-sh.sang.pp.ua:8080/project/gallery/page/20210121-223911-v2rayN-07.png)

##### 2.1.2.3 windows 10 配置全局代理

如果你是 windows 10 用户，你可以在设置中搜索 `代理`，然后配置代理服务器为刚才 v2rayN 上的 http 地址即可完成本机的代理。

![v2rayN-08](http://h5ai.home.frp-sh.sang.pp.ua:8080/project/gallery/page/20210121-223912-v2rayN-08.png)

##### 2.1.2.4 浏览器代理

如果你只是想要代理上网，不希望其他应用都走代理，那么可以只在浏览器中进行代理的设置。

不过遗憾的是主流的 chrome 和 edge 都没有相关设置的入口，所以需要依赖于浏览器插件。

chromium 内核的浏览器可以使用 [SwitchyOmega](https://chrome.google.com/webstore/detail/proxy-switchyomega/padekgcemlokbadohgkifijomclgjgif)，你可以先使用 windows 设置中的代理为浏览器下载好代理插件，或者通过使用安装包的方式安装插件（新版本的 chrome 似乎已经禁止大部分插件通过安装包的方式安装，因为试验过的方法最后都失败了，所以此处仅作参考），然后再关闭 windows 设置中的代理使用插件代理。

1. 安装好 SwitchyOmega 后，点击插件图标 -> `选项`

![](http://h5ai.home.frp-sh.sang.pp.ua:8080/project/gallery/page/20210121-221111-switchyomega-01.png)

2. 进入设置界面，点击 `新建情景模式`

![](http://h5ai.home.frp-sh.sang.pp.ua:8080/project/gallery/page/20210121-221111-switchyomega-02.png)

3. 取任意名称，新建一个 `代理服务器`

![](http://h5ai.home.frp-sh.sang.pp.ua:8080/project/gallery/page/20210121-221111-switchyomega-03.png)

4. 配置协议为 `HTTP` 代理服务器为 `127.0.0.1` 端口为 `1080` （根据之前 v2ray 给出的配置填写）

![](http://h5ai.home.frp-sh.sang.pp.ua:8080/project/gallery/page/20210121-221535-switchyomega-04.png)

5. 配置完点击保存后，切换到刚刚配置的代理服务器上则完成代理

![](http://h5ai.home.frp-sh.sang.pp.ua:8080/project/gallery/page/20210121-221732-switchyomega-05.png)

##### 2.1.2.5 局域网代理

使用局域网代理需要开启 `允许来自局域网的连接`，设置后，将需要使用代理的机器与 v2ray 运行的机器连入相同的网段（连入同一个 wifi）然后在连接选项中添加代理服务器。

以手机为例

![](http://h5ai.home.frp-sh.sang.pp.ua:8080/project/gallery/page/20210121-223913-v2rayN-09.png)
