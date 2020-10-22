---
draft: true
title: "[C++] 重载 Operator"
date: 2020-05-18T09:49:26+08:00
categories:
- cpp
tags:
- cpp
keywords:
- cpp
- operator
thumbnailImage: images/gaussian.jpg
thumbnailImagePosition: right
coverImage: images/gaussian.jpg
coverMeta: in
coverSize: partial
metaAlignment: center
# Summary 
---

<!--more-->

```
_________
\_   ___ \     .__         .__
/    \  \/   __|  |___   __|  |___
\     \____ /__    __/  /__    __/
 \______  /    |__|        |__|
        \/
```

# [Cpp] 重载 Operator

## 1. 重载 operator 示例

```cpp
#include <iostream>
#include <vector>

class test {
public:
    int v;
    test():v(0){}
    test(const int &a):v(a){}
    test(const test &t1):v(t1.v){}

    bool operator<(const test &t1) const {
        return (v < t1.v);
    }
}
```


