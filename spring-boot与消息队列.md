### 消息队列

> 消息队列使用消息将应用连接起来。这些消息通过像Rabbitmq这样的消息代理服务器在应用程序之间路由。   — 《Rabbitmq in action》

目前常用的消息队列大概有：

1. RabbitMQ：相对重量级高并发的情况，比如数据的异步处理、任务的串行执行；

2. Kafka：基于Pull模式来处理，具有很高的吞吐量，一般用来进行日志的存储和收集；

3. Redis：轻量级高并发，实时性要求高的情况，比如缓存、秒杀，及时的数据分析（ELK日志分析框架，使用的就是Redis）

   ​


#### 相关概念：

- AMQP消息路由必须有三部分：交换器、队列和绑定

- 消息：有效载荷(payload)+标签(label)。有效载荷就是你想要传输的数据，它可以是任何内容，一个JSON数组等；标签则描述了有效载荷，并且RabbitMQ用它来决定谁将获取消息的拷贝。

- 信道(Channel)：无论是发布消息、订阅队列或是接收消息，都是通过信道完成的。

  - 为什么不直接通过TCP连接发送AMQP命令呢？

    对操作系统来说建立和销毁TCP会话是非常昂贵的开销。

    ![理解AMQP信道和连接](http://wangnan.tech/post/rabbitMQ-producer-consumer/01.png)

- 交换机(Exchange)：用于接收消息并且转发到绑定的队列，但不会做存储。如果没有Queue bind到Exchange的话，它会直接丢弃掉Producer发送过来的消息。在启用ack模式后，交换机找不到队列会返回错误。交换机有四种类型：Direct,Topic,Headers,Fanout

  - Direct:direct类型的行为是“先匹配，再投送”。即在绑定时设定一个**routing_key**,消息的**routing_key** 匹配时，才会被交换机投送到绑定的队列中。

    **Direct Exchange**

    Direct Exchange是Rabbitmq默认的交换机模式，也是最简单的模式，根据key全文匹配去寻找队列。

    ![Direct Exchange图示](https://pic1.zhimg.com/v2-46fa7fea1bdd1ae6959f43125a6b9194_b.png)

    第一个 X - Q1 就有一个 binding key，名字为 orange； X - Q2 就有 2 个 binding key，名字为 black 和 green。当消息中的 路由键 和 这个 binding key 对应上的时候，那么就知道了该消息去到哪一个队列中。

  - Topic:按规则转发消息(最灵活)

    Topic Exchange转发消息主要是根据通配符。队列和交换机的绑定会定义一种路由模式，那么，通配符就要在这种路由模式和路由键之间匹配后才能转发消息。

    - 路由键必须是一串字符，用.隔开，如argreements.us或者agreements.eu.stockholm等
    - 路由模式必须包含一个星号(*)，主要要用于匹配路由键指定位置的一个单词，比如说，一个路由模式是这样子：agreements..b.\*，那么就只能匹配路由键是这样子的：第一个单词是 agreements，第四个单词是 b。 井号（#）就表示相当于一个或者多个单词，例如一个匹配模式是agreements.eu.berlin.#，那么，以agreements.eu.berlin开头的路由键都是可以的.
      - *表示一个词
      - \#表示0火或多个词

  - Headers:设置header attribute参数类型的交换机

    headers 也是根据规则匹配, 相较于 direct 和 topic 固定地使用 routing_key , headers 则是一个自定义匹配规则的类型. 在队列与交换器绑定时, 会设定一组键值对规则, 消息中也包括一组键值对( headers 属性), 当这些键值对有一对, 或全部匹配时, 消息被投送到对应队列。

    headers交换器允许你匹配AMQP消息的header而非路由键。

  - Fanout:转发消息到所有的绑定队列

    Fanout Exchange 消息广播的模式，不管路由键或者是路由模式，会把消息发给绑定给它的全部队列，如果配置了routing_key会被忽略。 就是我们熟悉的广播模式或者订阅模式，给Fanout交换机发送消息，绑定了这个交换机的所有队列都收到这个消息

- 路由键(Routing Key)：消息到交换机时，交换机会转发到对应的队列中，那么究竟转发到哪个队列，就要根据该路由键。

- 绑定(Binding)：交换机需要和队列相绑定，多对多的关系。

- 如果至少有一个消费者订阅了队列的话，消息会立即发送给这些订阅的消费者。但是如果消息到达了无人订阅的队列的情况下，消息会在队列中等待。一旦有消费者订阅到该队列，那么队列上的消息就会发送给消费者。

- 消息是如何分发的？

  当Rabbit队列拥有多个消费者时，队列收到的消息将以循环的方式发送给消费者。每条消息只会发送给一个订阅的消费者。

- 消息Ack的好处：

  - 保证消息送达 - 当消费者收到一条消息，然后确认之前从Rabbit断开连接（或者从队列上取消订阅），RabbitMQ会认为这条消息没有分发，然后重新分发给下一个订阅的消费者。
  - 防止应用消息堵塞而导致过载 - 如果应用程序有bug而忘记确认消息的话，Rabbit将不会给消费者发送更多消息了，这是因为在上一条消息被确认之前，Rabbit会认为这个消费者并没有准备好接收下一条消息；当处理的消息内容非常耗时时，则你的应用程序可以延迟确认该消息，直到消息处理完成，这样就可以防止Rabbit持续不断的消息涌向你的应用而导致过载。

- 队列设置的一下参数：

  - exclusive - 如果设置为true的话，队列将变成私有的，此时只有你的应用程序才能够消费队列消息。当你想要限制一个队列只有一个消费者的时候

  - auto-delete - 当最后一个消费者取消订阅的时候，队列就会自动移除。

    如果你需要临时队列只为一个消费者服务的话，请结合使用auto-delete和exclusive。当消费者断开连接时，队列就被移除了。




#### 源码部分解读

`ConnectionFactory` : The central component for managing a connection to the RabbitMQ broker.





#### Spring-boot集成RabbitMQ

> Spring-boot提供了spring-boot-started-amqp项目对消息各种支持

Maven依赖：

```java
<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

配置文件:

```java
spring.application.name=spirng-boot-rabbitmq

spring.rabbitmq.host=localhost
spring.rabbitmq.port=5672
spring.rabbitmq.username=admin
spring.rabbitmq.password=123456
  
# 其他配置项
spring.rabbitmq.dynamic 是否创建AmqpAdmin bean，默认为true
spring.rabbitmq.listener.acknowledge-mode 指定Acknowledge模式
spring.rabbitmq.listener.auto-startup 是否在启动时就启动mq，默认true
spring.rabbitmq.listener.concurrency 指定最小的消费者数量
spring.rabbitmq.listener.max-concurrency 指定最大的消费者数量
spring.rabbitmq.listener.prefetch 指定一个请求能处理多少个消息，如果有事务的话，必须大于等于transaction数量
spring.rabbitmq.listener.transaction-size 指定一个事务处理的消息数量，最好是小于等于prefetch的数量
spring.rabbitmq.requested-heartbeat 指定心跳超时，0为不指定
spring.rabbitmq.ssl.enabled 是否开启SSL，默认false
spring.rabbitmq.ssl.key-store 指定持有的SSL certificate的key store的路径
spring.rabbitmq.ssl.key-store-password 指定访问key store的密码
spring.rabbitmq.ssl.trust-store 指定持有SSL certificates的Trust sore
spring.rabbitmq.ssl.trust-store-password 指定访问Trush store的密码
```




附相关链接：

[简书：SpringBoot 使用(四): 消息队列使用](http://www.jianshu.com/p/048e954dab40)

[知乎：Spring Boot系列(八)：RabbitMQ详解](https://zhuanlan.zhihu.com/p/25069044?refer=dreawer)

[Segmentfault:SpringBoot应用之消息队列](https://segmentfault.com/a/1190000004401870#articleHeader0)