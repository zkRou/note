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

## 基于注解

## 基于XML

> 使用注解来映射会使代码显得更加简洁，然而对于稍微复杂一点的语句，Java注解就力不从心了，并且会显得更加混乱。因此，如果你需要完成很复杂的事情，那么最好使用XML来映射语句。

### 动态SQL

#### if

```xml
<select id="findUsersWithNicknameLike" resultType="User">
  SELECT * FROM USER WHERE deleted = 'false'
  <if test="nickname!=null">
    AND nickname like #{nickname}
  </if>
</select>
```

#### choose, when, otherwise

choose类似于Java中的switch语句。当第一个when满足时，就只执行第一个when中的条件；当when中的条件都不满足时，就会执行默认的，也就是otherwise的语句。

```xml
<select id="findActiveBlogLike" resultType="Blog">
  SELECT * FROM blog WHERE state = 'ACTIVE'
  <choose>
    <when test="title != null">
      AND title like #{title}
    </when>
    <when test="author != null and author.name != null">
      AND authro_name like #{author.name}
    </when>
    <otherwise>
      AND featured = 1
    </otherwise>
  </choose>
```

#### trim, where, set

- where

```xml
<select id="findActiveBlogLike" resultType="Blog">
  SELECT * FROM BLOG
  <where>
    <if test="state != null">
      state = #{state}
    </if>
    <if test="title != null">
      AND title like #{title}
    </if>
    <if test="author != null and author.name != null">
      AND author_name like #{author.name}
    </if>
  </where>
</select>
```

`where`元素只会在至少有一个子元素的条件返回SQL子句的情况下才去插入"WHERE"子句。而且，若语句的开头为"AND"或"OR",where元素也会将它们去除。

- trim

`trim`有四个属性：prefix、prefixOverrides、suffix、suffixOverrides，四个属性的作用位置依次是：prefix、prefixOverrides、suffixO

  prefix：前缀，增加一些指定的内容
  prefixOverrides：前缀重写，删除指定的内容
  suffix：后缀，增加一些指定的内容
  suffixOverrides：后缀重写，删除指定的内容

**示例一**

```xml
select * from user
<trim prefix="where" prefixOverrides=" and | or ">
  <if test=" name!=null name.length()>0 ">
    and name=#{name}
  </if>
  <if test=" gender!=null and gender.length()>0 ">
    and gender=#{gender}
  </if>
</trim>
```

>prefix：在前面增加where
 prefixOverrides：删除第一个and或者or

假设name和gender的值都不为null的话打印的SQL为：
```sql
select * from user where name ='xx' and gender = 'xx'
```

**示例二**

```xml
update user
<trim prefix="set" suffixOverrides="," suffix=" where id=#{id} ">
  <if test=" name!=null and name.length>0 ">
    name=#{name},
  </if>
  <if test=" gender!=null and gender.length()>0 ">
    gender=#{gender},
  </if>
</trim>
```

> prefix：在前端增加set
  suffixOverrides：删除最后一个逗号
  suffix：在最后增加where id = #{id}

假如说name和gender的值都不为null的话打印SQL为：
```sql
update user set name='xx',gender='xx' where id='xx'
```

- set

```xml
<update id="updateAuthorIfNecessary">
  update author
    <set>
      <if test="username!=null">username=#{username},</if>
      <if test="">
```


## 相关链接

[Mybatis文档](http://www.mybatis.org/mybatis-3/) - 官方文档写的也是很简陋了

[tk.mybatis项目文档](https://github.com/abel533/Mapper/wiki)
