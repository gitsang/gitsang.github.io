---
title: "Java Lambda 表达式"
date: 2020-05-14T14:53:28+08:00
categories:
- programing
- java
tags:
- java
keywords:
- Java Lambda 表达式
thumbnailImage: images/errigal.jpg
thumbnailImagePosition: right
metaAlignment: center
coverImage: images/errigal.jpg
coverMeta: in
coverSize: partial
---
<!--more-->

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