---
title: "Googletest Quick Start"
description: ""
lead: ""
date: 2019-12-15T14:53:28+08:00
lastmod: 2019-12-15T14:53:28+08:00
draft: false
images: []
menu: 
  docs:
    parent: "cpp"
weight: 100
toc: true
---
<!--more-->

## 1. Install

### 1.1 Get package

Using git clone.

```sh
git clone https://github.com/google/googletest.git
```

Using wget to achieve tar package, you can find newest tar address at `README.md`.[^2] [^3]

```sh
wget https://github.com/google/googletest/archive/release-1.10.0.tar.gz
tar -zxvf release-1.10.0.tar.gz
```

### 1.2 Make & Install

Both `cmake` or `cmake3` is ok.

Read `googletest/googletest/README.md` for more infomation.

```
$ cd googletest
$ mkdir build
$ cd build
$ cmake ..
$ make
$ sudo make install
```

### 1.3 Attention when cmake processing

```
-- The C compiler identification is GNU 4.8.5
-- The CXX compiler identification is GNU 4.8.5
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Found PythonInterp: /usr/bin/python (found version "2.7.5")
-- Looking for pthread.h
-- Looking for pthread.h - found
-- Looking for pthread_create
-- Looking for pthread_create - not found
-- Looking for pthread_create in pthreads
-- Looking for pthread_create in pthreads - not found
-- Looking for pthread_create in pthread
-- Looking for pthread_create in pthread - found
-- Found Threads: TRUE
-- Configuring done
-- Generating done
-- Build files have been written to: /root/project/googletest-release-1.10.0/build
```

In the above log, pthread_create is finally found in pthread, so it is not found on the way, but it should be fine.

## 2. Simple Test

### 2.1 Testing code

- `add.cc`

```cpp
#include <iostream>

int add(int a, int b) {
    return a + b;
}
```

- `testAdd.cpp`

```cpp
#include <gtest/gtest.h>

extern int add(int a, int b);

TEST(testCase, test0) {
    EXPECT_EQ(add(2, 3), 5);
}

int main(int argc, char **argv) {
    testing::InitGoogleTest(&argc, argv);
    return RUN_ALL_TESTS();
}
```

### 2.2 Compile and Run

```sh
$ g++ add.cpp testAdd.cpp -lgtest -lpthread
$ ./a.out
[==========] Running 1 test from 1 test case.
[----------] Global test environment set-up.
[----------] 1 test from testCase
[ RUN      ] testCase.test0
[       OK ] testCase.test0 (0 ms)
[----------] 1 test from testCase (0 ms total)

[----------] Global test environment tear-down
[==========] 1 test from 1 test case ran. (0 ms total)
[  PASSED  ] 1 test.
```

`-lgtest` is used for linking `libgtest.a`

If using `-lgtest_main`, you can coding without main() function[^4]

---
## Q & A

### Q1: Undefined reference when linking with googletest

#### A:

If you downloaded the gtest source directly and used the make install from the downloaded gtest repository it may have installed header files under `/usr/local/include/gtest`.

If you later use apt-get (yum install) to install the gtest, it installs the header files under `/usr/include/gtest`.

If the version installed from the debian package is newer, your Makefile can pick up the older header files from `/usr/include` and give you link errors even though you are correctly linking the new `libgtest.a` archive.

The solution is to look for `/usr/local/include/gtest` and `/usr/include/gtest` to see if they both exist. If they do then delete the older directory.

If `/usr/include/gtest` is the older directory, you may want to remove it by uninstalling the `libgtest-dev` package.[^1]

### Q2: Could not find CMAKE_ROOT

```
CMake Error: Could not find CMAKE_ROOT !!!
CMake has most likely not been installed correctly.
Modules directory not found in
/usr/local/share/cmake-3.11
cmake version 3.11.0-rc4

CMake suite maintained and supported by Kitware (kitware.com/cmake).
```

#### A:

```sh
$ wget https://cmake.org/files/v3.12/cmake-3.12.0-rc1.tar.gz
$ tar -zxvf cmake-3.12.0-rc1.tar.gz
$ cd cmake-3.12.0-rc1
$ ./bootstrap
$ gmake
$ gmake install
```

### Q3: Compile error

#### A:

add `-std=c++11` when compile

```
$ g++ *.cpp -lgtest -lpthread -std=c++11
```


## Reference

[^1]: [Undefined reference when linking with googletest - Stack Overflow](https://stackoverflow.com/questions/39207940/undefined-reference-when-linking-with-googletest)

[^2]: [Google Testの使い方 - Qiita](https://qiita.com/shohirose/items/30e39949d8bf990b0462)

[^3]: [使用 Google Test 测试框架 - Senlin's Blog](http://senlinzhan.github.io/2017/10/08/gtest/)

[^4]: [第一个gtest程序 - 码农教程](http://www.manongjc.com/article/68895.html)
