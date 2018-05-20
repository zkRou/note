---
title: Spring AOP
date: 2018/04/22
tags: [spring, aop]
---

## A -> B -> C -> D

传统的编程方式是`垂直化的编程`，即`A->B->C->D`这么下去，一个逻辑完毕之后执行另外一段逻辑。但是`AOP`提供了另一种思路，它的作用是在业务逻辑不知情(即业务逻辑不需要做任何的改动)的情况下对业务代码的功能进行增强。基于AOP不会破坏原来程序逻辑，因此它可以很好的对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分的耦合度降低，提高程序的可重用性，同时提高了开发的效率。

## AOP - 面向切面编程(Aspect Oriented Programming)

### # 引入AOP依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

### # 添加注解

切面类 - `@Aspect`

### # 通知类型

- 前置通知 `@Before` ,方法被调用前调用

  - `JointPoint`：可以获得通知的签名信息，如目标方法名、目标方法参数信息等
  - `RequestContextHolder`：获取请求信息、Session信息
- 后置最终通知
- 后置返回通知
- 后置异常通知
- 环绕通知