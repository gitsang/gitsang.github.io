---
draft: true
title: "map 容器"
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

# Introduce

map is a container use key to automatically sort data, the value of each 
element is independent of the key and can be changed directly.

## Head should include

```cpp
#include <map>
using namespace std;
```

## Template prototype

```cpp
template <class Key, class Type, class Traits = less<Key>, 
          class Allocator = allocator<pair<const Key, Type>>>

```
- Key：存储在 map 容器中的关键字的数据类型
- Type：储存在 map 容器中的数据值的数据类型
- Traits：提供比较两个元素的关键字来决定它们在map容器中的相对位置。
  可选,默认值 `less<key>`
- Allocator：它代表存储管理设备。
  可选,默认值`allocator<pair<const Key, Type>>`

map 容器有以下的特点:
- 它是一个相关联的容器,它的大小可以改变,它能根据关键字来提高读取数据能力。
- 它提供一个双向的定位器来读写取数据。
- 它已经根据关键字和一个比较函数来排好序。
- 它的每一个元素的关键字都是惟一的。
- 它是一个模板,它能提供一个一般且独立的数据类型。
