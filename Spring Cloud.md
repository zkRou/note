---
title: Spring Cloud
author: Kairou Zeng
date: 2018/05/01
---

### Spring Cloud

微服务而言，有六个基本必须实现的：

1. 进程通讯
2. 服务注册与发现
3. 负载均衡
4. 配置中心
5. 熔断器
6. 网关路由


Spring Cloud针对服务注册与发现进行了一层抽象，并提供了三种实现：

1. Eureka 
2. Consul
3. Zookeeper


#### Spring Cloud Eureka

> 基于Netflix Eureka做了二次封装(它既包含了服务端组件，也包含了客户端组件)，主要负责完成微服务架构中的服务治理功能。Spring Cloud通过为Eureka增加了Spring Boot风格的自动化配置，使用者只需要通过简单引入依赖和注解配置就能让Spring Boot构建的微服务与Eureka服务治理体系进行整合。
>
> Eureka实现了服务治理机制强调的CAP原理中的AP。
>
> C - Consistency 即数据一致性
>
> A - Available 即可用性 
>
> P - Partition 即分区容错性

- **服务注册**

  服务注册中心 — 每个服务单元都向服务注册中心登记自己的服务，将主机、端口号、版本号、通信协议等一些附加信息告知注册中心，注册中心会维护一套服务清单，并且需要以心跳的方式去检测清单中的服务是否可用。

  - 高可用服务注册中心

    即将自己作为服务，向其他服务注册中心注册自己，形成一组相互注册的服务注册中心。

  ​

- **服务发现**

  通过服务名发起请求调用（而不在通过实例地址+端口的方式）。因此，调用方需要向服务注册中心咨询服务，并获取所有服务的实例清单，以实现对具体服务实例的访问。


##### 基础架构

- **服务注册中心** 

  Eureka提供的服务端，提供服务注册与发现的功能。

- **服务提供者**

  提供服务的应用，可以是Spring Boot应用，也可以是其他技术平台且遵循Eureka通信机制的应用。它将自己提供的服务注册到Eureka，以供其他应用发现。

- **服务消费者** 

  消费者应用从服务注册中心获取服务列表，从而使消费者可以知道去何处调用其所需要的服务。如使用Ribbon、Feign的消费方式。

 很多时候，客户端既是服务提供者也是服务消费者。


`注`：

​	服务注册中心引入的依赖为

```java
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-eureka-server</artifactId>
</dependency>
```

​	服务提供者引入的依赖为：

```java
<dependency>
   <groupId>org.springframework.cloud</groupId>
   <artifactId>spring-cloud-starter-eureka</artifactId>
</dependency>
```



#### Spring Cloud Ribbon

> 是一个基于HTTP和TCP的`客户端负载均衡 `工具，基于Netflix Ribbon实现。Ribbon可以在通过客户端中配置的ribbonServerList服务端列表去轮询访问以达到均衡负载的作用。

![img](https://sfault-image.b0.upaiyun.com/401/149/4011492799-57a185a3dfbaa)

`服务器端负载均衡`:(其中包含硬件负载均衡与软件负载均衡)对用户透明，用户请求到负载均衡服务器，真正的应用服务器是由LB服务器分发控制的。

![img](https://sfault-image.b0.upaiyun.com/375/822/3758222551-57a18562548ad_articlex)

`客户端负载均衡`:它是客户端软件的一部分，客户端获知到可用的服务器列表按一定的均衡策略，分发请求。



通过Spring Cloud Ribbon的封装，我们在微服务架构中使用客户端负载均衡调用非常简单，只需要如下两步：

1. `服务提供者` 只需要启动**多个**服务实例并注册到一个注册中心或是多个相关的服务注册中心；

2. `服务消费者` 直接通过调用被**@LoadBalanced**注解修饰过的**RestTemplate**来实现面向服务的接口调用。

   RestTemplate会使用Ribbon的自动化配置，同时通过配置@LoadBalanced还能够开启客户端负载均衡。

##### 与Eureka结合

当在Spring Cloud的应用中，同时引入Spring Cloud Ribbon和Spring Cloud Eureka依赖时，会触发Eureka中实现的对Ribbon自动化配置。

当Ribbon与Eureka联合使用时，ribbonServerList会被DiscoveryEnabledNIWSServerList重写，扩展成从Eureka注册中心中获取服务端列表。同时它也会用NIWSDiscoveryPing来取代IPing，它将职责委托给Eureka来确定服务端是否已经启动。

也可以通过参数配置的方式来禁用Eureka对Ribbon服务实例的维护实现。在配置文件中加入如下参数：



```java
ribbon.eureka.enabled=false
```





相关资料：
[简书 - 微服务架构的服务与发现-Spring Cloud](http://www.jianshu.com/p/5eac16e9804a)

