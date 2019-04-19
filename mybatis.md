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

## springboot下使用mybatis

引入`MyBatis-Spring-Boot-Starter`模块后，将自动提供以下功能：
1. 自动检测现有的数据源。springboot会自动加载spring.datasource.*的相关配置，数据源会自动注入到`SqlSessionFactory`中，`SqlSessionFactory`会自动注入到`Mapper`中。
2. 将创建并注册一个`SqlSessionFactory`的实例，使用`SqlSessionFactoryBean`作为输入传递该`DataSource`。
3. 将创建并注册`SqlSessionFactory`的`SqlSessionTemplate`实例。
4. 自动扫描`Mapper`，将它们连接到`SqlSessionTemplate`并将它们注册到Spring上下文，以便将它们注入到bean中。

> 就是说，使用了该Starter之后，只需要定义一个DataSource即可，它会自动创建使用该DataSource的SqlSessionFactoryBean以及SqlSessionTemplate，会自动扫描你的Mappers，连接到SqlSessionTemplate，并注册到Spring上下文中。

## 依赖

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

## 注解

- @Mapper：Marker interface for MyBatis mappers 

```Java
@Mapper
public interface StudentMapper {

    List<StudentPO> findAll();

    List<StudentClazzVO> findAllWithClazz();
}
```

- @MapperScan：Use this annotation to register MyBatis mapper interfaces when using Java Config.


指定扫描Mapper类的包的路径。
```Java
@SpringBootApplication
@MapperScan(basePackages = "com.kris.springbootmybatisxmldemo.mapper")
public class SpringbootMybatisXmlDemoApplication {

	public static void main(String[] args) {
		SpringApplication.run(SpringbootMybatisXmlDemoApplication.class, args);
	}

}
```

## 配置

 MyBatis-Spring-Boot的应用配置参数也存储在`application.properties`或`application.yml`中。

 以下Mybatis配置均使用`mybatis`前缀。具体配置源码可见`org.mybatis.spring.boot.autoconfigure.MybatisProperties`
 | property | description |
 | --- | --- | 
 | config-location | mybatis的主配置文件(mybatis-config.xml)所在位置 |
 | check-config-location | 指示是否对MyBatis xml配置文件执行状态检查。 |
 | mapper-locations | mapper xml配置文件所在位置 |
 | type-aliases-package | 指定bean在哪个包里 |
 | type-aliases-super-type | typeAliasesPackage设置的是个很大的范围，不是具体的entity所在的包，里面有许多entity之外的东西。如果配置了typeAliasesSuperType(例如XX.BaseEntity)，就只扫描所有继承了BaseEntity的类 |
 | type-handlers-package | 扫描typeHandlers的包 |
 | executor-type | MyBatis执行器：ExecutorType.SIMPLE-默认执行器，这个类型不做特殊的事情，它只为每个语句创建一个PreparedStatement；ExecutorType.REUSE-这种类型将重复使用PreparedStatement；ExecutorType.BATCH-这个类型批量更新 |
 | configuration-properties | MyBatis配置的外部化属性。 指定的属性可以用作MyBatis配置文件和Mapper文件的占位符。 |
 | configuration | 一个MyBatis配置bean。 关于可用的属性，请参阅[MyBatis参考页面](http://www.mybatis.org/mybatis-3/configuration.html#settings)。 注意此属性不能与配置位置（config-location）同时使用。 |


## 基于注解使用

### 注解

- @Select
- @Result：修饰返回结果集，关联实体类属性和数据库字段一一对应，如果实体类属性和数据库属性名保持一致，就不需要这个属性来修饰
- @Insert
- @update
- @Delete

## 基于XML使用

> 使用注解来映射会使代码显得更加简洁，然而对于稍微复杂一点的语句，Java注解就力不从心了，并且会显得更加混乱。因此，如果你需要完成很复杂的事情，那么最好使用XML来映射语句。

### 标签

- mapper

  namespace属性用于与业务中的mapper关联。
  select中的id属性用来与业务中mapper的方法进行关联，查询到结果就会返回给该方法。
  `#{}`是占位符，表示需要动态获取的数据。
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.kris.springbootmybatisxmldemo.mapper.ClazzMapper">

    <select id="findAll" resultType="com.kris.springbootmybatisxmldemo.entity.po.ClazzPO">
        select * from t_clazz;
    </select>
</mapper>
```

- sql 

  >重复SQL预计永远不可避免，<sql>标签就是用来解决这个问题的。

  ```xml
  <sql id="sql1">id,name,age,gender</sql>

  <select id="getPerson" parameterType="int" resultType="Person">
    select
    <include refid="sql1"></include>
    from Person where id=#{id}
  </select>
  ```

  \<sql>：定义重复代码，id-sql代码块名称
  \<include>：引用sql，refid-需要应用sql代码块的id

  ```xml
  <sql id="studentSql">
      ${alias}.id as id,
      ${alias}.name as name,
      ${alias}.gender as gender,
      ${alias}.age as age,
      ${alias}.clazz_id as clazzId
  </sql>

  <select id="findAll" resultType="com.kris.springbootmybatisxmldemo.entity.po.StudentPO">
      select
      <include refid="studentSql">
          <property name="alias" value="t" />
      </include>
      from t_student t;
  </select>
  ```

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

`trim`有四个属性：prefix、prefixOverrides、suffix、suffixOverrides，四个属性的作用位置依次是：prefix、prefixOverrides、suffixOverrides、suffix

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
      <if test="password!=null">password=#{password},</if>
      <if test="email!=null">email=#{email},</if>
      <if test="bio!=null">bio=#{bio}</if>
    </set>
  where id=#{id}
</update>
```

set元素会动态前置SET关键字，同时也会删掉无关的逗号。

#### foreach

```xml
<select id="selectPostIn" resultType="domain.blog.Post">
  SELECT * FROM post p WHERE id IN
  <foreach item="item" index="index" collection="list" open="(" separator="," close=")">
    #{item}
  </foreach>
</select>
```


## 相关链接

[Mybatis文档](http://www.mybatis.org/mybatis-3/) - 官方文档写的也是很简陋了

[tk.mybatis项目文档](https://github.com/abel533/Mapper/wiki)

[mybatis-spring-boot-starter文档](http://www.mybatis.org/spring-boot-starter/mybatis-spring-boot-autoconfigure/)
