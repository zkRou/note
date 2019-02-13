---
title: Spring-retry重试机制
author: Kairou Zeng
date: 2019/02/01
tags: [spring]
---

## 框架介绍

`spring retry`是从`spring batch`独立出来的一个功能，主要实现了重试和熔断，提供了自动并重复调用操作执行失败的能力。

### 使用场景

调用第三方接口或消息队列及一些网络抖动、连接超时等网络异常，此时就需要重试。（区块链中提交交易发生失败时，也可以考虑用重试机制，降低服务接入方交易失败的概率）

### 依赖

```xml
<dependency>
    <groupId>org.springframework.retry</groupId>
    <artifactId>spring-retry</artifactId>
</dependency>
```

`spring retry`用到了aspect增强，因此可能需要引入以下依赖：

```xml
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
</dependency>
```

### 常用注解

**@EnableRetry**：开启retry的拦截，一般在启动类（如：Application.class）上注解

**@Retryable**：注解在需要重试的方法上，被注解的方法发生异常时会重试，参数说明：

- value：抛出指定的异常才会重试，默认为空

- include：和value一样，默认为空，当exclude也为空时，默认所有异常

- exclude：指定不处理的异常

- maxAttempts：最大尝试次数，默认3次

- backoff：重试等待策略，默认使用@Backoff，@Backoff的delay-指定延迟后重试，默认为1000L；multiplier-指定延迟倍数，默认为0，表示固定暂停1秒后进行重试。比如delay=5000L,multiplier=2时，第一次重试为5秒后，第二次为10秒，第三次为20秒。

```java
@Retryable(
      value = {SQLException.class}, 
      maxAttempts = 2,
      backoff = @Backoff(delay = 5000L, multiplier=1.5)
      )
```

**@Recover**：用于`@Retryable`重试失败后的处理方法。当重试到达指定次数时，被注解的方法被回调，但需要注意只有入参类型和发生异常的类型一致时才会回调。

### 关键类

**RetryTemplate**：`RetryTemplate`实现了`RetryOperations`接口，简化了操作。重试操作被封装在`RetryCallback`接口的实现中，并使用提供的其中一种`execute()`方法执行。

- **RetryOperations**：定义实现的基本的重试操作。

```java
package org.springframework.retry;
import org.springframework.retry.support.DefaultRetryState;

public interface RetryOperations {

	<T, E extends Throwable> T execute(RetryCallback<T, E> retryCallback) throws E;

	<T, E extends Throwable> T execute(RetryCallback<T, E> retryCallback, RecoveryCallback<T> recoveryCallback) throws E;

	<T, E extends Throwable> T execute(RetryCallback<T, E> retryCallback, RetryState retryState) throws E, ExhaustedRetryException;

	<T, E extends Throwable> T execute(RetryCallback<T, E> retryCallback, RecoveryCallback<T> recoveryCallback, RetryState retryState)
			throws E;

}
```

- **RetryCallback**：`RecoveryCallback`是`execute()`的一个参数，它是一个接口，允许插入在失败时需要重试的业务逻辑。

- Configure a `RetryTemplate` bean in `@Configuration` class：

    ```java
    @Configuration
    public class AppConfig{
        @Bean
        public RetryTemplate retryTemplate(){
            RetryTemplate retryTemplate = new RetryTemplate();

            FixedBackOffPolicy fixedBackOffPolicy = new FixedBackOffPolicy();
            fixedBackOffPolicy.setBackOffPeriod(2000L);
            retryTemplate.setBackOffPolicy(fixedBackOffPolicy);

            SimpleRetryPolicy retryPolicy = new SimpleRetryPolicy();
            retryPolicy.setMaxAttempts(2);
            retryTemplate.setRetryPolicy(retryPolicy);
        }
    }
    ```

    `RetryPolicy`决定了重试操作的时间。`SimpleRetryPolicy`用来固定次数的重试。

    `BackOffPolicy`用于控制重试之间的间隔。``



## 实例

## 相关链接

[spring-retry github](https://github.com/spring-projects/spring-retry)

