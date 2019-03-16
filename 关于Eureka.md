---
title: 关于Eureka
author: Krirou Zeng
date: 2017/10/01
---

#### 关于Eureka

Eureka，是一个RESTful服务，用来定位运行在AWS域（Region）中的中间层服务。

Eureka由两个组件组成：Eureka服务器和Eureka客户端。Eureka服务器用作服务注册服务器。Eureka客户端是一个java客户端，用来简化与服务器的交互、作为轮询负载均衡器，并提供服务的故障切换支持。

目前Netflix只提供了java客户端。因为使用的是http协议，如果您要用其他语言来同服务器交互，你完全可以根据服务器提供的接口基于http自行实现一个客户端。

#### 跨语言

虽然Eureka是用Java编写，但它会将所有注册信息和心跳连接地址都暴露为HTTP REST接口，客户端实际是通过HTTP请求与Server进行通讯的，因此，Client完全可以使用其他语言进行编写，只需要即时调用注册服务、注销服务、获取服务列表和心跳请求的HTTP REST接口即可。


[Eureka与Client的通讯机制](https://github.com/Netflix/eureka/wiki/Understanding-Eureka-Peer-to-Peer-Communication)


两种选择：

1. 通过API的方式进行集成   -  Eureka HTTP API : [Eureka REST operations](https://github.com/Netflix/eureka/wiki/Eureka-REST-operations)

   服务实例使用POST请求注册网络地址，每30S必须使用PUT方法更新注册表，使用HTTP DELETE请求或实例超时来注销。客户端使用HTTP GET请求接受注册服务实例信息。

2. 当使用Spring Cloud时，可以使用Sidecar(Spring cloud netflix sidecar)进行多语言支持 - [Sidecar文档](http://projects.spring.io/spring-cloud/spring-cloud.html#_polyglot_support_with_sidecar)

   [使用Sidecar将Node.js引入Spring Cloud](https://zhuanlan.zhihu.com/p/22684127)


