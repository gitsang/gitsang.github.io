---
title: SpringMVC 及其注解
slug: sprint-mvc-and-annotation
description: This document provides an overview of the Model-View-Controller (MVC) architecture, detailing the roles of View, Controller, Service, and DAO layers in system design. It discusses how these layers function to achieve high cohesion and low coupling, facilitating ease of control and resource allocation.
date: "2020-05-14T14:53:28+08:00"
lastmod: "2025-05-12T10:43:59+08:00"
weight: 100
categories:
  - "java"
tags:
  - "MVC"
  - "SpringMVC"
  - "Controller"
  - "Service"
  - "DAO"
  - "Annotation"
  - "Java"
---

<!-- markdown-front-matter -->

## 1. MVC

MVC 指 Model - View - Controller

分层式为了实现“高内聚，低耦合”，易于控制，扩展和分配资源

Model 层一般再分为 DAO 层和 Service 层

### 1.1 View 层

表示层：jsp、html 等编写，为界面的展示

View 层和 Controller 层耦合度较高，也可以看作一个整体进行开发

@Component

### 1.2 Controller 层

控制层：接收客户端的请求，然后调用 Service 层业务逻辑，获取到数据，再传递数据给表示层展示

- @Controller

  注解控制层，告诉 SpringMVC 的 dispatcherServlet 这是一个 Controller 然后被 dispatcherServlet 的上下文所管理，并完成他的依赖注入

- @RestController

  相当于 @Controller 和 @ResponseBody 的组合注解

- @RequestMapping

  在类上使用 `@RequestMapping("/user")` 告诉 SpringMVC 该 Controller 会拦截 /user/\* 路径下所有 URL

  在方法上使用 `@RequestMapping(value = "login.do", method = RequestMethod.POST)` 使该方法负责处理 /usr/login.do 这个 URL 的 POST 请求

  `@RequestMapping` 使用 method 参数，可以使用 `@PostMapping` 或 `@GetMapping` `@PutMapping` `@DelMapping` `@PatchMapping` 替代

- @RequestParam / @PathVariable / @Param

  在方法的参数前绑定以上 3 种注解，负责把请求传入的参数绑定到方法中的参数上

```java
@Controller
@RequestMapping("/user")
public class UserController {

    @Autowired
    private UserService userService;

    /* 本方法负责处理 /usr/login.do 这个 URL 传入的 POST 请求 */
    @RequestMapping(value = "login.do", method = RequestMethod.POST)
    /* 自动序列化成 json */
    @ResponseBody
    public ServerResponse<User> login(
        /* 将请求传入参数绑定到 方法的参数上 */
        @RequestParam("username") String username,
        @RequestParam("password") String password,
        HttpSession session) {

        ServerResponse<User> response = iUserService.login(username, password);

        if(response.isSuccess()) {
            session.setAttribute(Const.CURRENT_USER, response.getData());
        }

        return response;
    }
}
```

### 1.3 Service 层

业务层：调用 DAO 层，实现解耦，利于通用业务的独立性和复用性

主要负责业务模块的逻辑应用设计

先设计 Service 接口，再设计其实现的类，接着在 Spring 的配置文件中配置其实现的关联

这样可以直接在应用中调用 Service 接口来进行业务处理

- @Service

  注解业务层，并将其申明为一个 Bean

```java
/**
 * UserService 类，Bean 名为 userService
 */
@Service("userService")
public class UserService {}
```

### 1.4 DAO 层

持久层：或数据访问层，实现对数据库的访问

DAO 层的设计首先是设计 DAO 的接口，然后再 Spring 的配置文件中定义此接口的实现类

这样可以直接在模块中调用此接口进行数据业务的处理，而不用关心接口的具体实现是哪个类

DAO 层的数据源配置，以及有关数据库连接的参数都在 Spring 的配置文件中配置

- @Repositoy
  注解数据访问层，告诉 SpringMVC 这是一个数据访问层，并将其申明为一个 Bean

```java
/**
 * UserDaoImpl 类实现 UserDao 接口，Bean 名称为 userDao
 */
@Repository("userDao")
public class UserDaoImpl implements UserDao {
}
```

---

# 参考

https://blog.csdn.net/zyq11223/article/details/78187389

https://blog.csdn.net/zdwzzu2006/article/details/6053006

https://blog.csdn.net/gg12365gg/article/details/51345601

https://blog.csdn.net/qq_39299341/article/details/79809381

https://blog.csdn.net/u010412719/article/details/69710480
