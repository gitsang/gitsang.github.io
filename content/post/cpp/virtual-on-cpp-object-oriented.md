---
title: 'C++ 面向对象中的虚（Virtual）'
description: '在 C++ 面向对象中，虚（virtual）是个重要的概念，他充分体现了面向对象实现中的继承和多态这两大特性'
slug: virtual-on-cpp-object-oriented
date: 2019-10-09T14:53:28+08:00
lastmod: 2020-05-28T15:22:06+08:00
weight: 1
categories:
  - C++
tags:
  - C++
  - virtual
---

## 1. 虚函数

### 1.1 简述

所谓虚函数是指：在类中希望被重写(override)的虚构的函数。也就是说 C++ 可以在派生类(derived class)中通过重写基类(based class)的虚函数来实现对基类虚函数的覆盖(override)

### 1.2 常见用法

最常见的用法就是：声明基类的指针，指向任意一个子类对象，调用相应虚函数，就调用了子类重写的函数。由于编写基类时候并不能确定将被调用的是那个派生类的函数，因此被称为“虚”函数。

如果不使用虚函数，则使用基类指针时，将总是被限制在基类函数本身，无论如何都无法调用到子类重写的函数。

### 1.3 代码示例

```cpp
#include <iostream>

class Base {
public:
    Base() {
    }

public:
    void print() {
        std::cout << "Base" << std::endl;
    }

    virtual void vprint() {
        std::cout << "vBase" << std::endl;
    }
};

class Derived : public Base {
public:
    Derived() {
    }

public:
    void print(){
        std::cout << "Derived" << std::endl;
    }

    void vprint() {
        std::cout << "vDerived" << std::endl;
    }
};

int main() {
   Base *p1 = new Base();
   p1->print();
   p1->vprint();

   Derived *p2 = new Derived();
   p2->print();
   p2->vprint();

   Base *p3 = new Derived();
   p3->print();
   p3->vprint();

   return 0;
}
```

代码中定义了一个基类 Base，并定义了一个函数 print() 和一个虚函数 vprint()，派生类 Derived 继承自 Base，并重写了 print 和 vprint 两个函数。

main 中分别 new 了 Base 和 Derived 对象，并调用自身的函数，这结果是很好预知的，一定是

```
Base
vBase
Derived
vDerived
```

之后定义了 基类指针 p3 并将其指向派生类，输出结果是：

```
Base
vDerived
```

这里就可以注意到基类指针调用函数 print() 时，实际上调用的是基类自身的 print()，即使这个指针已经指向了其派生类 Derived。

### 1.4 结果解释

这是由于 C++ 在编译时，内部成员函数一般都是静态加载的，编译器对于非虚函数他的调用地址是写死的，会将其定义类的函数地址写到调用语句上，这就是静态联编。只有在编译器遇到虚函数时才会将调用修改为寄存器间接寻址，即为动态联编。

因此，p3 虽然指向了派生类，但编译时仍然会给调用写上一个 Base::print() 的地址，即使编译器此时知道 p3 指向的并不是 Base，这是由编译逻辑决定的。

虽然你也可以不用虚函数，而是直接定义一个派生类的对象来调用派生类的方法，但这样就已经不是一个接口了，这就不是多态了。

### 1.5 总结

其实你也不必知道这么多的细节，你只要知道如果你想要仅仅暴露一个基类接口来实现多态，那么只需要为基类函数加上 virtual 标识符，然后用派生类重写该函数，最后将基类指针指向派生类就可以了。

### 1.6 附录

- 使用 g++ 生成汇编代码

```
g++ -S -fverbose-asm -g t_virtual.cpp -o t_virtual.s
as -alhnd t_virtual.s > t_virtual.as
```

- `p3->print()` 的汇编

```as
movq    -40(%rbp), %rax
movq    %rax, %rdi
call    _ZN4Base5printEv # 地址标号直接寻址，跳转到 Base 类的 print
```

- `p3->vprint()` 的汇编

```as
movq    -40(%rbp), %rax
movq    (%rax), %rax
movq    (%rax), %rax
movq    -40(%rbp), %rdx
movq    %rdx, %rdi
call    *%rax           # 间接寻址
```

## 参考

[^1][^2][^3][^4][^5][^6][^7][^8]

[^1]: [C++构造/析构函数中的多态(二)](http://codemacro.com/2014/09/06/necessary-dtor/)
[^2]: [浅谈 C++多态性](https://blog.csdn.net/Hackbuteer1/article/details/7475622)
[^3]: [C++多态--虚函数 virtual 及 override](https://blog.csdn.net/i_chaoren/article/details/77281785)
[^4]: [C++学习:虚函数,纯虚函数,虚继承,虚析构函数](https://blog.csdn.net/qq_29924041/article/details/73522256)
[^5]: [C++ Virtual 详解](https://blog.csdn.net/ring0hx/article/details/1605254)
[^6]: [C++中 virtual（虚函数）的用法](https://blog.csdn.net/foreverhuylee/article/details/34107615)
[^7]: [虚函数的深入理解](https://www.cnblogs.com/zsq1993/p/5804332.html)
[^8]: [为什么不直接用子类引用指向子类对象，而用父类引用指向子类对象](https://bbs.csdn.net/topics/391846458)
