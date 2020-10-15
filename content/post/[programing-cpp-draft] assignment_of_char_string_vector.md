---
draft: true
title: "char 的赋值"
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

# assignment of char

```c
char a = '1';
char b[5] = "aasf";
char c[] = "adfadage";

char* str1 = new char[10];
str1 = "adggwxc";
/* warning: deprecated conversion from string constant to ‘char*’ [-Wwrite-strings] */

char* str2 = "adsfew";
/* warning: deprecated conversion from string constant to ‘char*’ [-Wwrite-strings] */

char* str3 = new char[10]; strcpy(str3, "1sdfgg");
```

# assignment of string

# assignment of vector