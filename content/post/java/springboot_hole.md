---
title: "Springboot 踩坑记录"
description: ""
lead: ""
date: 2020-04-29T14:53:28+08:00
lastmod: 2020-04-29T14:53:28+08:00
draft: false
images: []
menu:
  docs:
    parent: "java"
weight: 100
toc: true
---

### Configuration Annotation Proessor not found in classpath

1. 使用 @ConfigurationProperties 报错
2. spring boot1.5 以上版本@ConfigurationProperties 取消 location 注解，只能使用 @PropertySource
3. 但使用 @PropertySource 依然出现报错

官方解决方案，Maven 引入依赖

```xml
<dependency>
   <groupId> org.springframework.boot </groupId>
   <artifactId> spring-boot-configuration-processor </artifactId>
   <optional> true </optional>
</dependency>
```

参考：https://blog.csdn.net/w05980598/article/details/79167826

### java.lang.IllegalStateException: Ambiguous mapping. Cannot map 'xxx' method

```log
at com.xxx.springbootconfiguration.SpringbootConfigurationApplication.main
    (SpringbootConfigurationApplication.java:10) [classes/:na]
Caused by: java.lang.IllegalStateException: Ambiguous mapping.
    Cannot map 'configurationController' method
        com.xxx.springbootconfiguration.controller.configurationController#getUser()
        to {GET }: There is already 'configurationController' bean method
com.xxx.springbootconfiguration.controller.configurationController#getInfo() mapped.
```

#### 原因：

controller 层 @GetMapping 重复使用了两个相同的路径

```java
    @GetMapping
    public void getInfo() {
        ...
    }

    @GetMapping
    public void getUser() {
        ...
    }
```

两个 @GetMapping 都没有指定路径，才会有这样一条报错（已经被 getInfo map 了）

```log
to {GET }: There is already 'configurationController' bean method
com.xxx.springbootconfiguration.controller.configurationController#getInfo() mapped.
```

#### 解决方法:

为 @GetMapping 添加值，保险一点两个都加，只加一个也可以

```java
    @GetMapping("/info")
    public void getInfo() {
        ...
    }

    @GetMapping("/user")
    public void getUser() {
        ...
    }
```
