---
title: Lambda 表达式
slug: lambda
description: This content demonstrates the use of anonymous inner classes and lambda expressions in Java, particularly in the context of threading and action listeners.
date: "2020-04-29T14:53:28+08:00"
lastmod: "2025-05-12T10:43:08+08:00"
weight: 100
categories:
  - "java"
tags:
  - "Java"
  - "Lambda Expressions"
  - "Anonymous Inner Classes"
  - "Threads"
  - "ActionListener"
---

<!-- markdown-front-matter -->

```java
    // 使用匿名内部类
    new Thread(new Runnable() {
        @Override
        public void run() {
            System.out.println("Hello from thread (Override)");
        }
    }).start();

    // 使用 lambda 表达式
    new Thread(
        () -> System.out.println("Hello from thread (lambda)")
    ).start();
```

```java
    // 使用匿名内部类
    button.addActionListener(new ActionListener() {
        @Override
        public void actionPerformed(ActionEvent e) {
            System.out.println("The button was clicked (Override)");
        }
    });

    // 使用 lambda 表达式
    button.addActionListener( (e) -> {
        System.out.println("The button was clicked (lambda)");
    });

```
