---
draft: true
title: "C++11中的线程"
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

## 1. C++11中的线程
#### 1.1 线程的管理
一个进程中至少存在一个线程，这个线程被称为主线程，我们可以在任意线程中创建线程类的实例。

每个线程都需要一个入口函数，当入口函数返回时，线程就会退出，主线程的入口函数为main()。

###### 1.1.1 线程的启动
线程的创建十分简单，我们只需创建一个线程类的实例，并为它传入一个可调用对象（lambda表达式；std::function；重载了调用运算符的类；成员函数；普通函数），就可以启动一个线程了：

```cpp
class Work {
public:
	void operator()() {
		std::cout << "callable object" << std::endl;
	}
};

void do_work {
	std::cout << "working" << std::endl;
}

void test() {
	std::thread worker0(do_work);	//普通函数
	worker0.detach();
	
	std::thread worker1([]() {		//lambda表达式
		std::cout << "lambda call" << std::endl;
	});
	worker1.detach();

	std::thread worker2(Work{});	//重载了调用运算符的类
	worker2.detach();
}
```




参考文献：

 1. [C++标准库多线程简介Part1](https://zhuanlan.zhihu.com/p/55836312)

https://www.cnblogs.com/wangguchangqing/p/6134635.html
https://blog.csdn.net/hitwengqi/article/details/8015646
https://www.runoob.com/cplusplus/cpp-multithreading.html