---
title: 共享Session
author: Krirou Zeng
date: 2017/09/01
---

#### 可用框架

spring session

kisso

spring security

#### 共享Session问题

HttpSession是通过Servlet容器创建和管理的，像Tomcat/Jetty都是保存在内存中的。

如果把web服务器搭建成分布式的集群，然后利用LVS和Nginx做负载均衡，那么来自同一用户的Http请求将有可能会被分到两个不同的web站点中。

如何保证不同的web站点能够共享一份Session数据？


#### Spring Session

##### 基本思路

将用户的session信息全部存放到一个redis数据库中，所有的session都从这个数据库拿。由于redis是一个内存数据库，数据信息读写是非常快速的。如此一来，多个Tomcat，共用一个redis数据库，即实现了session的共享问题。

spring-session要访问redis，spring-session依赖spring-data-redis

##### Spring Session对HTTP的支持

Spring Session对HTTP的支持是通过标准的servlet filter来实现的，这个filter必须要配置为拦截所有的web应用请求，并且它应该是filter链中的第一个filter。Spring Session filter会确保随后调用`javax.servlet.http.HttpServletRequest`的`getSession()`方法时，都会返回Spring Session的`HttpSession`实例，而不是应用服务器默认的HttpSession

###### 配置Spring Session

- 搭建用于Spring Session的数据存储

- 将Spring Session的jar/maven添加到wen应用中

- 将Spring Session filter添加到web应用的配置中

  （可以通过Spring boot的自动配置来实现，只需要在Spring boot的配置类上使用@EnableRedisHttpSession注解接可以了）

  ```java
  @SpringBootApplication
  @EnableRedisHttpSession
  public class ExampleApplication{
    
    public static void main(String[] args){
        StringApplication.run(ExampleApplication.class, args);
    }
  }
  ```

  EnableRedisHttpSession的核心是使用了import方式导入了RedisHttpSessionConfiguration.class配置类。该配置类通过@Bean注解，像Spring容器中注册了一个SessionRepositoryFilter(SessionRepositoryFilter的依赖关系：SessionRepositoryFilter->SessionRepository->RedisTemplate->RedisConnectionFactory)

  EnableRedisHttpSession可以：

  - 配置session过期时间maxInactiveIntervalInSeconds，默认是30分钟
  - 设置redisFlushMode，即session更新策略，有ON_SAVE[默认]（在调用SessionRepository#save(org.springframework.session.Session)时，在response commit前刷新缓存）、IMMEDIATE（只要有任何更新就会刷新缓存）
  - session默认存储在redis的key是 “spring:session:”，但如果有多个系统同时使用一个redis，则会冲突，此时应该配置redisNamespace值，配置后，其key为spring:session:<redisNamespace>:

  ​

- 配置Spring Session如何选择session数据存储的链接

  （Spring Session自带了对Redis的支持，相关Redis的配置可以添加到application.yml文件中）

  ```java
  spring.redis.host=localhost
  spring.redis.password=secret
  spring.redis.port=6379
  ```

  ​

###### 一些分析

`Tomcat`下的：ServletRequestWrapper、HttpServletRequestWrapper

借助以上这些包装类就能编写代码来拓展HttpServletRequest ,重载返回HttpSession的方法，让它返回由外部存储所提供的实现。



`Spring Session项目`下：

- SessionRepositoryRequestWrapper：

  Spring Session项目定义了扩展自标准HttpServletRequestMrapper的类，用来重载HttpServletRequest中与session相关的方法。

  getSession()方法中，Spring session项目不再将调用委托给应用服务器，而是实现自己的逻辑，返回由外部数据存储作为支撑的HttpSession实例。实现过程：

  - 检查是不是已经有session了，如果有则返回，否则检查当前的请求中是否有session id
  - 如果有的话，将会根据这个session id，从它的SessionRepository中加载session
  - 如果session repository中没有session，或者当前请求中没有当前session id与请求关联的话，则它会创建一个新的session，并将其持久化到session repository中

- SessionRepositoryFilter

  该类实现了Servlet Filter接口。

  doFilterInternal()方法中，创建了SessionRepositoryRequestWrapper对象(封装请求对象)和SessionRepositoryResponseWrapper对象(封装相应对象)，然后调用其余的filter链。这里，关键在于当这个filter后面的应用代码执行时，如果要获得session的话，得到的将会是Spring Session的HttpServletSession实例，它是由后端的外部数据存储作为支撑的。

- HttpSessionStrategy

- CookieHttpSessionStrategy

  使用HTTP cookie将请求与session id关联（默认是基于CookieHttpSessionStrategy实现（SpringHttpSessionConfiguration.java））

- HeaderHttpSessonStrategy

  HTTP header将请求与session关联



附：

[通过Spring Session实现新一代的Session管理](http://www.infoq.com/cn/articles/Next-Generation-Session-Management-with-Spring-Session)



