---
title: Curl 和 libcurl 快速入门
slug: curl-quick-start
description: Learn how to install curl and libcurl on Linux, compile and use them in C/C++ applications, and understand their command and library functions for HTTP communication.
date: "2019-10-15T14:53:28+08:00"
lastmod: "2025-05-09T19:18:13+08:00"
weight: 100
categories: 
- "Programming"
- "Software Installation"
tags: 
- "curl"
- "libcurl"
- "Linux"
- "C++"
- "HTTP"
- "network programming"
- "installation"

<!-- markdown-front-matter auto -->
---

## 1. 安装 curl 和 libcurl

### 1.1 使用包管理工具安装

linux 机器一般自带 curl 工具，如果没有可以通过 `apt/yum/pkg install curl` 的方式安装

同样的，可以通过 `apt/yum/pkg install libcurl` 的方式安装 curl 库

这种方式安装的好处是简单且稳定，缺点则是其版本可能较低

### 1.2 源码编译方式

官网: http://curl.haxx.se/

如需最新版可到官网获取下载地址[^1] [^2]

1. 下载源码

```
wget http://curl.haxx.se/download/curl-7.65.1.tar.gz
tar -zxf curl-7.65.1.tar.gz
cd curl-7.65.1
```

2. 初始化配置

一般无需设置直接使用默认配置即可，**建议不要修改安装路径**，否则后续还需自行添加 include 路径，若需指定安装路径，使用 `--prefix` 参数

```
./configure --prefix=/usr/local/curl
```

其余的配置的使用可通过 `./configure --help` 查看

建议安装之前先查看当前目录下的 `README`、`GIT-INFO`、`INSTALL` 文件，许多问题都能在此找到答案

3. 编译安装

```
make
make install
```

4. 检查版本

```
curl --version
```

版本信息与安装版本相符则到此步骤已经安装成功

5. 将 curl 命令加入环境变量（如果按照默认路径安装则无需执行此步骤）

仅对本会话起作用，若需永久生效需要在 `.bash_profile`、`.bashrc` 等文件里添加

```
export PATH=$PATH:/usr/local/curl/bin
```

6. 可能还需要将 include 目录复制到 `/usr/include` 下（如果按照默认路径安装则无需执行此步骤）

```
cp -r /usr/local/curl/include/curl /usr/include
```

当然也可以用软链接，或编译时指定 include 路径

```
ln -s /usr/local/curl/include/curl /usr/include/curl
```

## 2. C / C++ 调用 libcurl

到 `/usr/local/lib/` 或指定的安装路径，即可查看到安装好的库文件

若要include库文件可如下编写

```c
#include <curl/curl.h>
```

可根据[参考2](https://blog.csdn.net/qianghaohao/article/details/51684862)中代码测试 libcurl

在编译时要加入 `-lcurl` 参数，如 `g++ main.cpp -o main -lcurl`

## 3. curl 命令

以下命令为 curl 与 localhost 通讯进行上传和下载文件[^4]

```
# curl -v -X PUT http://localhost/doc/1.txt -T 2.txt
# curl -v -X GET http://localhost/doc/1.txt -o 2.txt
```

选项 `-v` 或 `--verbose` 为显示详细操作信息，建议刚开始使用时加上，能为调试提供很大帮助。

其余选项的使用可以使用 `curl --help` 或 `man curl` 查看帮助信息，每个选项都有详细的说明。（[中文翻译](http://man.linuxde.net/curl)）

curl命令能为之后curl库的使用提供参考基础，建议在进行curl代码编写前先使用curl命令实现，有些时候的bug不是代码造成的，有可能本身curl就无法建立连接。

## 4. curl 库的调用流程

libcurl主要提供了两种发送http请求的方式，分别是Easy interface方式和multi interface方式，前者是采用阻塞的方式发送单条数据，后者采用组合的方式可以一次性发送多条数据

libcurl传输任务流程如下（其中最重要的是3和4步）：[^3]

- 1 调用`curl_global_init()`初始化libcurl
- 2 调用`curl_easy_init()`得到easy interface型指针（句柄）
- **3 调用`curl_easy_setopt()`设置传输选项**
- **4 根据`curl_easy_setopt()`实现回调函数**
- 5 调用`curl_easy_perform()`传输数据
- 6 调用`curl_easy_cleanup()`清空句柄
- 7 调用`curl_global_cleanup()`释放内存

```c
#include <curl/curl.h>
#include <string>
#include <iostream>
using namespace std;

int httpPutFile() {
    CURL* curl = NULL;
    CURLcode result = CURLE_OK;
    string url = "http://10.200.100.50:8080";

    FILE* inputFile = fopen("./put.txt", "rb");
    string outputFilename = "/1.txt";
    url += outputFilename;

    //HttpHead
    struct curl_slist* headers = NULL;
    headers = curl_slist_append(headers, "endPoint:http://10.200.100.50:8080");
    headers = curl_slist_append(headers, "accessKeyId:id123");
    headers = curl_slist_append(headers, "secretAccessKey:key123");
    headers = curl_slist_append(headers, "Authorization:auth123");

    //Init
    curl_global_init(CURL_GLOBAL_ALL);
    curl = curl_easy_init();

    //Option
    curl_easy_setopt(curl, CURLOPT_URL, url.c_str());
    curl_easy_setopt(curl, CURLOPT_VERBOSE, 1);
    curl_easy_setopt(curl, CURLOPT_TIMEOUT, 2);
    curl_easy_setopt(curl, CURLOPT_HTTPHEADER, headers);
    curl_easy_setopt(curl, CURLOPT_INFILE, inputFile);
    //curl_easy_setopt(curl, CURLOPT_INFILESIZE, 87);
    curl_easy_setopt(curl, CURLOPT_PUT, 1);
    //curl_easy_setopt(curl, CURLOPT_CUSTOMREQUEST, "PUT");

    //Perform
    result = curl_easy_perform(curl);

    //Clean
    curl_slist_free_all(headers);
    curl_easy_cleanup(curl);
    curl_global_cleanup();

    return 0;
}

int httpGetFile() {

    CURL* curl = NULL;
    CURLcode result = CURLE_OK;
    string url = "http://10.200.100.50:8080";

    FILE* outputFile = fopen("./get.txt", "wb");
    string inputFilename = "/1.txt";
    url += inputFilename;

    //HttpHead
    struct curl_slist* headers = NULL;
    headers = curl_slist_append(headers, "endPoint:http://10.200.100.50:8080");
    headers = curl_slist_append(headers, "accessKeyId:id123");
    headers = curl_slist_append(headers, "secretAccessKey:key123");
    headers = curl_slist_append(headers, "Authorization:auth123");

    //Init
    curl_global_init(CURL_GLOBAL_ALL);
    curl = curl_easy_init();

    //Option
    curl_easy_setopt(curl, CURLOPT_URL, url.c_str());
    curl_easy_setopt(curl, CURLOPT_VERBOSE, 1);
    curl_easy_setopt(curl, CURLOPT_TIMEOUT, 2);
    curl_easy_setopt(curl, CURLOPT_HTTPHEADER, headers);
    curl_easy_setopt(curl, CURLOPT_WRITEDATA, (void*)outputFile);

    //Perform
    result = curl_easy_perform(curl);

    //Clean
    curl_slist_free_all(headers);
    curl_easy_cleanup(curl);
    curl_global_cleanup();

    return 0;
}

int main(int argc, char* argv[]) {

    string method = argv[1];

    if(method == "PUT" || method == "put") {
        httpPutFile();
    }else if(method == "GET" || method == "get") {
        httpGetFile();
    }else {
        cout << "Method error, please input \"PUT\" or \"GET\"." << endl;
    }
    cout << endl;

    return 0;
}
```

## 5. 参考

[^1]: [Linux CURL的安装和使用](https://blog.csdn.net/lifan5/article/details/7350154)

[^2]: [linux下编译安装libcurl(附使用示例)](https://blog.csdn.net/qianghaohao/article/details/51684862)

[^3]: [curl_easy_setopt-curl库的关键函数之一](https://www.cnblogs.com/lidabo/p/4583067.html)

[^4]: [C++ 用libcurl库进行http通讯网络编程](https://www.cnblogs.com/moodlxs/archive/2012/10/15/2724318.html)
