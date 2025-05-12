---
title: Understanding Closures in Programming
slug: closure
description: This content explores closures, clarifying their nature as entities that store functions and their environment, and discussing their distinction from objects and functions. It highlights the encapsulation aspect and how closures extend variable scope while being isolated from external states.
date: "2025-05-09T19:43:07+08:00"
lastmod: "2025-05-12T10:41:52+08:00"
weight: 1
categories:
  - "golang"
tags:
  - "closures"
  - "programming"
  - "function scope"
  - "lexical environment"
  - "object-oriented"
---

<!-- markdown-front-matter -->

## 1. 什么是闭包

所谓的闭包，其实就是**存储了函数及其关联环境的一个实体**，使其在脱离上下文时照常运行。

从字面上理解，闭包就是将函数封闭、打包（中华文化博大精深，闭包这两个字明显比 Closure 更加贴切）

封闭指的是：封闭外部状态，即内部环境无法访问到外部状态，或者说外部状态无法对内部产生影响

打包指的是：为了能够脱离外部环境而存在，要将需要用到的外部环境打包到自己的内部空间

我认为许多人在理解闭包时，仅仅理解到了闭包能够扩展变量的作用域这一层面，而忽略了闭包的封闭性（或者说是隔离性），

## 2. 如何获得闭包

通俗地说，获得闭包的方式就是**将函数作为值返回**

## 3. 闭包和对象 / 函数的区别

首先应该明确的是：闭包既非对象，也不是函数或作用域

> A closure is the combination of a function bundled together (enclosed) with references to its surrounding state (the lexical environment).[^1]
>
> 闭包是捆绑在一起（封闭）的函数与对其周围状态（词法环境）的引用的组合。

闭包应该是一个组合，在中英文 wiki 中分别被解释成了“结构体”和“记录”，但都不应该简单地将其理解为对象或者函数。

闭包与对象和函数的联系应该是：闭包相当于一个带状态的函数，闭包相当于只有一个函数的对象。[^2]

并且实际上对象系统能够基于闭包实现。[^3]

## 4. 参考

[^1]: [Closures - JavaScript | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures)

[^2]: [闭包和对象的关系 - Todd Wei - 博客园](https://www.cnblogs.com/weidagang2046/archive/2010/11/01/1865899.html)

[^3]: [Re: FP, OO and relations. Does anyone trump the others?. 29 December 1999 [2008-12-23]](http://okmij.org/ftp/Scheme/oop-in-fp.txt)
