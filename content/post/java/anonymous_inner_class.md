---
title: 匿名内部类
slug: anonymous-inner-class
description: This document provides examples of implementing anonymous classes, abstract class inheritance, anonymous method overriding, and adding execution statements in constructors in Java. It includes detailed code snippets and explanations for each scenario.
date: "2020-04-29T14:53:28+08:00"
lastmod: "2025-05-12T10:42:52+08:00"
weight: 100
categories:
  - "java"
tags:
  - "Java"
  - "Anonymous Classes"
  - "Interface Implementation"
  - "Abstract Classes"
  - "Method Overriding"
  - "Constructors"
  - "Code Examples"
---

<!-- markdown-front-matter -->

## 1. Example 1: 匿名接口实现类

1. 接口类：

```java
public interface InterfaceTest {
    String getName();
    String getAge();
}
```

2. new 创建匿名内部类

或相当于创建一个匿名的接口，继承 InterfaceTest 接口，然后创建一个该接口的实现类

```java
package test;

public class NewIterfaceDemo {

    public static void main(String[] args) {

        InterfaceTest itest = new InterfaceTest() {
            @Override
            public String getName() {
                return "Lisa";
            }

            @Override
            public String getAge() {
                return "18";
            }
        };

        System.out.println(itest.getName() + itest.getAge());
    }
}
```

相当于创建了一个 InterfaceTest 接口的实现类

```java
public class NewInterfaceTest implements InterfaceTest {

    @Override
    public String getName() {
        return "Lisa";
    }

    @Override
    public String getAge() {
        return "18";
    }

    public static void main(String[] args) {
        NewInterfaceTest nit = new NewInterfaceTest();
        System.out.println(nit.getName() + nit.getAge());
    }
}
```

## 2. Example 2: 抽象类的匿名继承类

```java
public abstract class AbsTest {
    abstract String getName();
    abstract String getAge();
}
```

```java
public class AbsDemo {

    public static void main(String[] args) {

        AbsTest absTest = new AbsTest() {

            @Override
            String getName() {
                return "Gilgamesh";
            }

            @Override
            String getAge() {
                return "25";
            }
        };

        System.out.println(absTest.getName() + absTest.getAge());
    }
}
```

相当于

```java
public class ExtAbsClass extends AbsTest{

    @Override
    String getName() {
        return "Gilgamesh";
    }

    @Override
    String getAge() {
        return "25";
    }

    public static void main(String[] args) {
        ExtAbsClass extAbsClass = new ExtAbsClass();
        System.out.println(extAbsClass.getName() + extAbsClass.getAge());
    }
}
```

## 3. Example 3: 对象匿名重写

对于非抽象得类，也可以通过 {} 重写类方法

```java
import java.util.HashMap;
import java.util.Map;

public class Test {
    public static void main(String[] args) {
        Map<String, String> map = new HashMap<String, String>() {
            @Override
            public String put(String key, String value) {
                key = "key_override";
                value = "value_override";
                return super.put(key, value);
            }
        };

        map.put("key", "value");
        System.out.println(map.get("key"));
        System.out.println(map.get("key_override"));
    }
}
```

输出结果：

```
null
value_override

Process finished with exit code 0
```

也相当于创建了一个新的类继承原本非抽象类，然后重写其中的方法，但新的类匿名，且这个方法被重写了的类仅对这一个对象有效。

## 4. Example 4: 为构造函数添加执行语句

在匿名类中使用 {}

```java
import java.util.ArrayList;
import java.util.List;

public class Test {

    public static void main(String[] args) {

        List<String> list = new ArrayList<String>() {
            {
                add("a");
                add("b");
            }
        };
        System.out.println(list.size());
    }
}
```

相当于实现匿名类的构造函数，当然这种写法并不规范（因为当定义一个集合后，向集合内添加元素却没有相应的取出操作，这个添加动作就没有什么意义了）

```java
import java.util.ArrayList;
import java.util.List;

public class MyArrayList<E> extends ArrayList<E> {
    private MyArrayList() {
        add("a");
        add("b");
    }

    @SuppressWarnings("unchecked")
    public static void main(String[] args) {
        List<String> list = new MyArrayList();
        System.out.println(list.size());
    }
}
```

## 5. 参考

https://blog.csdn.net/shenhaiyushitiaoyu/article/details/84142618?utm_source=distribute.pc_relevant.none-task
