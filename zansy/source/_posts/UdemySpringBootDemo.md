title: SpringBoot学习笔记
author: zansy
tags: 
  - Udemy
categories:
  - 上课笔记
date: 2019-08-08 13:05:31
---
Spring Boot is Spring's convention-over-configuration solution for creating stand-alone, production-grade Spring-based Applications that you can "just run". It is preconfigured with the Spring team's "opinionated view" of the best configuration and use of the Spring platform and third-party libraries so developer can get started with minimum fuss. Most Spring Boot applications need very little Spring configuration. 
<!--more-->

# 入门

## URL路由-注解

### RequestMapping: 地址映射

```java
@RestController
public class HelloController {
    @RequestMapping("/say")
    public String hello(){
        return "hello world";
    }
}
```

![映射结果](/images/image-20190808133256078.png)

- 也可以应用在根部（注意可以设置方法）：

```java
@RestController
@RequestMapping("/api")
public class HelloController {
    @RequestMapping(value = "/say", method = RequestMethod.GET)
    public String hello(){
        return "hello world";
    }
}
```

![注意地址栏的变更](/images/image-20190808133611628.png)

- 注解的另一种写法：`@GetMapping("/say")`，同理@PostMapping等
- @RestController和@Controller的区别：

```java
@Controller
//@RestController
@RequestMapping("/api")
public class HelloController {
    //@RequestMapping(value = "/say", method = RequestMethod.GET)
    @PostMapping("/say")
    public String hello(){
        return "hello world";
    }

    @GetMapping("/books")
    public String getAll(){
        return "books";
    }
}
```

这里的books返回的并不是字符串，而是模版。1. 需要在resources文件下的templates文件夹中加入books的同名html文件作为返回。2. 在pom.xml更新一句：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId >
    <artifactId >spring-boot-starter-thymeleaf</artifactId >
</dependency>
```

（或许需要在pom文件右击使其成为maven项目）这样之后访问的就是文件了：

![@Controller访问效果](/images/image-20190808144844976.png)

- 在方法名后加上@ResponseBody可以获得@RestController的效果。

```java
@Controller
//@RestController
@RequestMapping("/api")
public class HelloController {
    @GetMapping("/books")
    @ResponseBody
    public String getAll(){
        return "books";
    }
}
```

