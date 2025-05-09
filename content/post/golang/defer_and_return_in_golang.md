---
title: defer 和 return 的执行顺序陷阱
slug: defer-and-return-in-golang
description: Explore the nuances of using defer in Golang functions and its impact on named vs unnamed return values. This article provides a detailed explanation of how defer affects the return process.
date: "2020-08-28T16:08:25+08:00"
lastmod: "2025-05-09T18:41:04+08:00"
weight: 100
categories: 
- "Programming"
- "Golang"
- "Software Development"
tags: 
- "Golang"
- "defer"
- "return values"
- "programming functions"
- "error handling"

<!-- markdown-front-matter auto -->
---

Golang 中 defer 几乎被当作 try catch final 使用，但事实上 defer 对返回值的修改和 final 仍然有些一些微妙的不同

## 1. 问题的产生

先来看一段简短的代码

```go
// defer.go
func deferTest() string {
	s := "init"
	defer func() {
		s = "defer"
	}()
	return s
}

func main() {
	fmt.Println(deferTest())
}
```

我们知道 defer 会在 return 之前以先进后出的顺序执行，但是 `deferTest()` 返回的是 `init` 还是 `defer` 呢。

运行结果：

```sh
> go run defer.go
init
```

很显然，程序在 return 前会先执行 `s = "defer"` 然后再 return。

但是打印的结果却是 `init`。

是 defer 没有被执行吗？是 defer 晚于 return 执行吗？并不是，这里先不解释，稍微修改一下代码重新执行

```go
// defer.go
func deferTest() (s string) {
	s = "init"
	defer func() {
		s = "defer"
	}()
	return s
}

func main() {
	fmt.Println(deferTest())
}
```

```sh
> go run defer.go
defer
```

这个时候返回结果变成 `defer` 了，我们仅仅是给返回值加上了命名，defer 就将返回值改变了。

## 2. 解释

实际上，return 前确实会先执行 defer 的内容，但是返回值却不是任何时候都会在 defer 时被改变的。

return 的执行顺序应该是这样的：

1. 给 `返回值` 赋值

2. 调用 RET 指令，并传入 `返回值`
   - RET 先检查是否存在 defer，存在则逆序执行
   - RET 携带 `返回值` 退出函数

这里的第一步，若是匿名返回值，那么在给 `返回值` 赋值时，将会先声明一个 `返回值` (因为没有定义返回值的名称)

```go
返回值 := s
```

若是命名的返回值，则直接赋值（因为已经声明过了）

```go
s = s
```

这样，最后匿名返回值返回的是 `返回值`，而命名为 `s` 的返回值则返回 `s` 的值。

这也是为什么命名返回值之后可以直接 return 的原因，因为执行 `return s` 其实也就只是约等于多执行了一条没有意义的 `s = s` 而已，和 `return` 是一样的。

或者不严谨的说：golang 中 `func function() returnType {}` 的 returnType 其实并不像其他语言一样代表的是“返回值类型”，而是一个“匿名返回值”才对。
