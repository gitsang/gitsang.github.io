---
title: "[LIB] curl 快速入门"
date: 2019-10-15T14:53:28+08:00
categories:
- Library
tags:
- curl
thumbnailImage: images/port.jpg
thumbnailImagePosition: right
metaAlignment: center
coverImage: images/port.jpg
coverMeta: in
coverSize: partial
---
<!--more-->

## 1. 安装curl和libcurl
Linux部分机器自带curl，可以直接使用，但不会包含curl库，且有些版本过旧，所以仍然建议下载最新版本curl使用

###### （1）获得安装包（官网http://curl.haxx.se/）
本文列出不一定是当前最新版，到官网查询最新版本链接自行更改即可
```
 # wget http://curl.haxx.se/download/curl-7.65.1.tar.gz 
```
###### （2）解压
```
# tar -zxf curl-7.65.1.tar.gz
```
###### （3）进入解压后的目录
```
# cd curl-7.65.1
```
###### （4）配置文件
一般无需设置直接使用默认配置即可，**建议不要修改安装路径**，否则后续还需自行添加include路径，若需指定安装路径，使用--prefix参数
```
# ./configure --prefix=/usr/local/curl
```
其余的配置的使用可通过`./configure --help`查看

建议安装之前先查看当前目录下的README、GIT-INFO、INSTALL文件，许多不可预测的问题都能在此找到答案

###### （5）make
```
# make 
# make install 
```
###### （6）检查版本
```
# curl --version
```
版本信息与安装版本相符则到此步骤已经安装成功
###### （*）将curl命令加入环境变量
（如果按照默认路径安装则无需执行此步骤）

仅对本会话起作用，或者在.bash_profile、.bashrc文件里配置环境变量
```
# export PATH=$PATH:/usr/local/curl/bin
```
###### （*）添加curl.h到include路径
（如果按照默认路径安装则无需执行此步骤）
```
# cp -r /usr/local/curl/include/curl /usr/include
```
###### （*）C/C++调用libcurl

到/usr/local/lib/或指定的安装路径，即可查看到安装好的库文件

若要include库文件可如下编写
```c
#include <curl/curl.h>
```
可根据[参考文献2](https://blog.csdn.net/qianghaohao/article/details/51684862)中代码测试libcurl

需要注意在编译过程中一定要加入 -lcurl 参数，否则将出现链接错误，如`g++ main.cpp -o main -lcurl`

## 2. curl命令的简单实用

以下命令为curl与localhost通讯进行上传和下载文件
```
# curl -v -X PUT http://localhost/doc/1.txt -T 2.txt
# curl -v -X GET http://localhost/doc/1.txt -o 2.txt
```
选项`-v`或`--verbose`为显示详细操作信息，建议刚开始使用时加上，能为调试提供很大帮助。

其余选项的使用可以使用`curl --help`或`man curl`查看帮助信息，每个选项都有详细的说明。（[中文翻译](http://man.linuxde.net/curl)）

curl命令能为之后curl库的使用提供参考基础，建议在进行curl代码编写前先使用curl命令实现，有些时候的bug不是代码造成的，有可能本身curl就无法建立连接。

## 3. curl库的调用
libcurl主要提供了两种发送http请求的方式，分别是Easy interface方式和multi interface方式，前者是采用阻塞的方式发送单条数据，后者采用组合的方式可以一次性发送多条数据

libcurl传输任务流程如下（其中最重要的是3和4步）：
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


---
## 参考

 1. [Linux CURL的安装和使用](https://blog.csdn.net/lifan5/article/details/7350154)

 2. [linux下编译安装libcurl(附使用示例)](https://blog.csdn.net/qianghaohao/article/details/51684862)

 3. [curl_easy_setopt-curl库的关键函数之一](https://www.cnblogs.com/lidabo/p/4583067.html)
 
 4. [C++ 用libcurl库进行http通讯网络编程](https://www.cnblogs.com/moodlxs/archive/2012/10/15/2724318.html)