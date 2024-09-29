---
title: "结构体的定义方式"
description: ""
lead: ""
date: 2019-10-09T14:53:28+08:00
lastmod: 2019-10-09T14:53:28+08:00
draft: false
images: []
menu: 
  docs:
    parent: "cpp"
weight: 100
toc: true
---
<!--more-->

## 1. 使用typedef定义结构体

typedef用来定义新的数据类型，通常typedef与结构体的定义配合使用。使用typedef的目的使结构体的表达更加简练（所以说typedef语句并不是必须使用的）。 [^1] [^2] [^3] [^4]

### 1.1 定义一个名字为TreeNode的结构体类型（现在并没有定义结构体变量，并不占用内存空间）：

```cpp
struct TreeNode
{
        int Element;
        struct TreeNode* LeftChild;
        struct TreeNode* RightChild;
};
```

### 1.2 为结构体起一个别名Node，这时Node就等价于struct TreeNode

```c
typedef struct TreeNode Node;
```

结构体的定义和typedef语句可以连在一起写：

```c
typedef struct TreeNode
{
        int Element;
        struct TreeNode* LeftChild;
        struct TreeNode* RightChild;
}Node;
```

注意不要与“定义结构体类型的同时定义结构体类型变量”混淆：

### 1.3 使用typedef关键字定义结构体类型，定义结构体类型的同时定义结构体类型变量

```c
typedef struct student
{
        int age;
        int height;
}std;
//std相当于struct student	

struct student
{
        int age;
        int height;
}std1,std2;
//定义了student数据类型的结构体和std1、std2结构体变量
```

## 2. 使用typedef定义结构体指针

### 2.1 定义一个名为TreeNode的结构体，和指向该结构体类型的指针PtrToTreeNode（不使用typedef）：

```c
struct TreeNode
{
        int Element;
        struct TreeNode* LeftChild;
        struct TreeNode* RightChild;
};

struct TreeNode *PtrToTreeNode； //定义指针
```

### 2.2 使用typedef关键字用一个单词Node代替struct TreeNode，并定义指向该结构体类型的指针PtrToTreeNode：

```c
struct TreeNode
{
        int Element;
        struct TreeNode* LeftChild;
        struct TreeNode* RightChild;
};
typedef struct TreeNode Node;   //用Node代替struct TreeNode

Node *PtrToTreeNode;            //定义指针

/* 或可不定义别名直接定义结构体指针：
 * typedef struct TreeNode* PtrToTreeNode
 * 若使用智能指针：
 * typedef std::shared_ptr<TreeNode> PtrToTreeNode 
 */
```

### 2.3 将结构体的定义和typedef连在一起写，再次缩短代码：

```c
typedef struct TreeNode
{
        int Element;
        struct TreeNode* LeftChild;
        struct TreeNode* RightChild;
}Node;                          //定义结构体并用Node代替struct TreeNode
Node *PtrToTreeNode;            //定义指针
```

### 2.4 还可以继续缩短代码，直接定义了指向结构体类型的指针，但是这种写法没有为结构体起一个别名。

```c
typedef struct TreeNode
{
        int Element;
        struct TreeNode* LeftChild;
        struct TreeNode* RightChild;
} *PtrToTreeNode;               //直接定义指针
```

在定义结构体时，省略struct后面的结构体名也是可以的，但是由于没有名字，操作（如定义结构体变量）只能在定义的同时进行


## 参考

[^1]: [typedef关键字与结构体、结构体指针的定义 - 一路洒满阳光XD](https://blog.csdn.net/u013632190/article/details/47720703)

[^2]: [typedef struct和指针 - CN_L4EX](https://blog.csdn.net/u013814701/article/details/52996544)

[^3]: [typedef函数指针用法 - qll125596718](https://blog.csdn.net/qll125596718/article/details/6891881)

[^4]: [关于typedef的用法总结 - csyisong](https://www.cnblogs.com/csyisong/archive/2009/01/09/1372363.html)
