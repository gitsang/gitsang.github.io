---
draft: true
title: "头文件/类/对象"
date: 2020-05-14T14:53:28+08:00
categories:
- programing
- cpp
tags:
- cpp
keywords:
- cpp
thumbnailImage: images/sea.jpg
thumbnailImagePosition: right
metaAlignment: center
coverImage: images/sea.jpg
coverMeta: in
coverSize: partial
---
<!--more-->

## 1. 头文件
#### 1.1 头文件的包含
#### 1.2 头文件的防卫式声明

```cpp
#ifndef _COMPLEX_
#define _COMPLEX_
// 声明
#endif
```

## 2. 类与对象
对象（Object）是类（Class）的一个实例（Instance）

类可以将数据和函数封装在一起，其中函数表示了类的行为（或称服务）

类提供关键字public（公有）、protected（受保护）和private（私有）用于声明数据和函数的访问级别。







## 参考文献
[GeekBand C++面向对象高级编程（上）1](https://zhuanlan.zhihu.com/p/22831581)
[C++面向对象程序设计思想（精）](https://blog.csdn.net/qq_37018433/article/details/54631391)
[C++面向对象的设计思想——小结](https://www.cnblogs.com/alwayswangzi/p/6490437.html)



---
## C++11类型转换
```cpp
size_t realsize = size * nmemb;
std::string tmp(static_cast<const char*>(ptr), realsize);
out.append(tmp);
```
C++四种类型转换运算符：static_cast、dynamic_cast、const_cast和reinterpret_cast
http://c.biancheng.net/cpp/biancheng/view/3297.html