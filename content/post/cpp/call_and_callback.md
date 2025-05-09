---
title: 调用和回调函数
slug: call-and-callback
description: This article explains what callback functions are in C++ programming, how they differ from normal function calls, and provides insights into their usage and registration. Examples are provided to elucidate these concepts.
date: "2019-10-09T14:53:28+08:00"
lastmod: "2025-05-09T19:18:03+08:00"
weight: 100
categories: 
- "Programming"
- "C++"
tags: 
- "callback functions"
- "C++ programming"
- "function pointers"
- "system operations"
- "kernel programming"

<!-- markdown-front-matter auto -->
---

## 1. 回调函数简介

回调函数就是那些自己写的，但是不是自己来调，而是给别人来调用的函数。

消息响应函数就可以看成是回调函数，因为是让系统在合适的时候去调用。这不过消息响应函数就是为了处理消息的，所以就拿出来单做一类了。其实本质上就是回调函数。

比如按键事件，其实是个消息，你的函数比按键事件更早存在，所以你要将这个函数做为回调函数提交给系统， 然后系统在接收到按键事件后，再调用你的函数

但是回调函数不是只有消息响应函数一种，比如在内核编程中，驱动程序就要提供一些回调函数，当一个设备的数据读写完成后，让系统调用这些回调函数来执行一些后续工作。回调函数赋予程序员这样一种能力，让自己编写的代码能够跳出正常的程序控制流，适应具体的运行环境在正确的时间执行。

## 2. 回调和调用的关系

### 2.1 调用

调用就是用户传出参数让函数进行处理，或让函数执行某个动作
如以下操作就是调用，用户需要传入函数要求的参数，函数会返回一个传入参数中的最大值

```cpp
int mymax(int a, int b) { // 定义函数
    return a > b ? a : b;
}

int main() {
    int a = 10;
    int b = 20;
    mymax(a, b); // 调用函数
}
```

### 2.2 回调

#### 2.2.1 注册回调函数

回调就是用户编写函数对系统传入的参数进行处理，或在系统发出信号时执行某个操作，如：[^1] [^2]

```cpp
int mymax(int a, int b) { // 定义回调函数
    return a > b ? a : b;
}

int main() {
    system_operation sysop;
    sysop.max = mymax; // 注册回调函数
    return 0;
}
```

此时mymax并未由用户传入参数，而是等待系统传入。

上述代码中`system_operation`为虚构的结构体，结构体内声明了一个空的max函数，如果系统直接调用max函数不会执行任何内容。

需要用户将自己编写的函数注册到max函数上`sysop.max = mymax;`，注册成功后系统在调用到max函数时，就相当于调用了用户的mymax函数。注册回调意思就是把用户函数关联到系统函数

#### 2.2.2 回调函数作为参数

还有另一种形式的回调，是回调函数作为参数，如：

```cpp
#include "otherfunc.h"

static size_t mymax_callback(int a, int b, int &max) {
    max = a < b ? a : b; // 我就是要返回最小值给你
    return 0;
}

int main() {
    //函数原型 int othfunc_max(void* callbackfunction);
    othfunc_max(mymax_callback);
    return 0;
}
```

此时a, b, &max都是系统传入的参数，至于传的是什么此时并不需要关心，只需要关心怎么处理a, b, &max这三个参数就行，一般来说回调函数需要哪些参数，参数的作用等都会在系统的说明文档或头文件注释中给出，写回调时候根据文档写出符合需求的回调函数就行了。（当然只要你开心，只要把格式写对，功能不符合需求也行，手动狗头）

用户在调用othfunc_max时，只需传入对应的回调函数名称，系统会将a, b, &max传给mymax_callback函数，函数只给&max赋上a和b的最大值（其实是最小值），并未将max值返回（即使返回也不是返回给用户，用户能收到的仅仅是othfunc_max()的返回值）。[^3] [^4]

## 3. 参考

[^1]: [C++中回调函数(CallBack)的使用](https://blog.csdn.net/bzhxuexi/article/details/11767979)

[^2]: [C++ 回调函数理解 - clirus](https://blog.csdn.net/clirus/article/details/50350519)

[^3]: [【C++基础之八】函数指针和回调函数 - 偶尔e网事](https://blog.csdn.net/jackystudio/article/details/11720325)

[^4]: [回调函数中的参数列表是规定好的么 - TianYongwei](https://cnodejs.org/topic/58205fc7be0a73ad05489563)
