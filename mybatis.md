---
title: Mybatis
author: Kairou Zeng
date: 2019/03/17
tags: [ORM, Mybatis]
---

## 为什么写

一直以来都知道这个ORM框架（毕竟Java招聘页面上，70%以上都写着熟悉该框架）。但除了大三实习那会儿用过`Hibernate`，想着ORM框架应该也大致差不多吧，很巧的是毕业后的工作也一直没有用到`Mybatis`。最近加入了一个新的合作项目，对方项目里用的是`Mybatis`，因此还是学习一下吧。

## 框架介绍

Mybatis是一个持久层框架(ORM)，它支持定制化SQL、存储过程以及高级映射。`Mybatis`避免了几乎所有的JDBC代码和手动设置参数以及获取结果集。`Mybatis`可以使用简单的XML或注解来配置和映射原声信息，将接口和Java的POJOs(Plain Old Java Objects,普通的Java对象)映射成数据库中的记录。

## 使用 & 依赖

### Mybatis

```xml
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>XXX</version>
</dependency>
```

### Springboot + Mybatis

```xml
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>XXX</version>
</dependency>
```

### tk.mybatis - 通用Mapper

是一个个人开发并开源的Mapper项目，是给使用`Mybatis`的开发人员提供一些通用的Mapper集合，省去了编写大量的基本sql。通用Mapper就是为了解决单表增删改查，基于`Mybatis`的插件。开发人员不需要编写sql，不需要再dao中增加方法，只要写好实体类，就能支持相应的增删改查方法。

```xml
<dependency>
  <groupId>tk.mybatis</groupId>
  <artifactId>mapper</artifactId>
  <version>XXX</version>
</dependency>
```

```xml
<dependency>
  <groupId>tk.mybatis</groupId>
  <artifactId>mapper-spring-boot-starter</artifactId>
  <version>XXX</version>
</dependency>
```

## 相关链接

[Mybatis文档](http://www.mybatis.org/mybatis-3/) - 官方文档写的也是很简陋了

[tk.mybatis项目文档](https://github.com/abel533/Mapper/wiki)
