---
title: "重载 Operator"
description: ""
lead: ""
date: 2020-05-18T09:49:26+08:00
lastmod: 2019-10-09T14:53:28+08:00
draft: true
images: []
menu: 
  docs:
    parent: "cpp"
weight: 1000
toc: true
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


