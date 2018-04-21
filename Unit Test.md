---
title: 单元测试
author: Kairou Zeng
date: 2018-04-16
tags: 单元测试
---

# Unit Test

----
## Mockito

- 什么是`mock测试`?什么是`mock对象`?

  `mock对象`就是在调试期间用来作为真实对象的替代品
  
  `mock测试`就是在测试过程中，对那些不容易构建的对象用一个虚拟对象来代替测试的方法
- BDDMockito

----
## MockMvc

`MockMvc`实现了对Http请求的模拟，能够直接使用网络的形式，转换到Controller的调用。

`perform`:请求

`MockMvcRequestBuilders`:进行url的请求

`andExpect`:对Controller类、调用方法、视图和model的预期设置

`andDo`:进行这次请求的执行

`andReturn`:返回

| 方法名 | 说明 | 使用 |
| --- | --- | --- |
| andExpect | 判断结果是否满足某个ResultMatcher对象 | mockMvc.perform(post("/form)).andExpect(statue().isOk()).andExpect((redirectedUrl(“/person/1”)) |
| andDo | 针对结果做某种处理，如打印等 | mockMvc.perform(get(“/form”)).andDo(print()) |
| andReturn | 返回MvcResult对象，可以获取Request、Response以及返回的数据等信息。 | |

andExpect方法中的Matcher可以使用`MockMvcResultMatchers`来进行构建,其构建的`Matcher`包括：

| 方法 | 说明 | 
| --- | --- |
| request | 获取RequestResultMatchers类型的Matcher,可对Attribute、SessioAttribute等进行判断 | 
| handle |


Mockito通过方法when()、thenReturn()等方法可以对方法进行打桩，让后续方法按照自己的数据桩来返回，达到了隔离依赖的效果。

----
## Spring Boot Test

Spring Boot中引入单元测试很简单，依赖如下：
```maven
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

`spring-boot-test`包含核心项目，`spring-boot-test-autoconfigure`支持测试的自动配置。

大多数开发人员只会使用`spring-boot-starter-test`来导入包括Spring Boot测试模块以及JUnit、AssertJ、Hamcrest和其他一些有用的库。

如果使用`spring-boot-starter-test`，您会发现以下提供的库：
- JUnit - 单元测试Java应用程序的标准
- Spring Test & Spring Boot Test - Spring Boot应用的实用和集成测试支持
- AssertJ - 断言库
- Hamcrest - 对象匹配库(也叫约束或断言)
- Mockito - Java Mock框架
- JSONassert - JSON断言库
- JsonPath - 用于Json的XPath

### Controller(API)单元测试

使用`MockMvc`，就可以不必启动工程就能测试这些接口。

`MockMvc`实现了对Http请求的模拟，能够直接使用网络的形式，转换到Controller的调用，这样可以使得测试速度快、不依赖网络环境，而且提供一套验证的工具，这样可以使得请求的验证统一而且很方便。

(感觉对Controller的单元测试就像使用Postman进行的接口测试)

### Testing Spring Boot applications

- Spring Boot Test 常用注解

  - @SpringBootTest

    为`SpringApplication`创建上下文并支持SpringBoot特性
    ```java
    @SpringBootTest(classes = StartUpApplication.class, webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
    ```
    - `classes`属性指定启动类
    - `SpringBootTest.WebEnvironment.RANDOM_PORT`经常和测试类中`@LocalServerPort`一起在注入属性时使用。会随机生成一个端口号。
      - `webEnvironment`属性有以下4种：
        1. Mock - 加载WebApplicationContext并提供Mock Servlet环境。使用此注释时，Embedded servlet容器不会启动。`@AutoConfigureMockMvc`为`MockMvc`基于您的应用程序。
        2. RANDOOM_PORT - 加载EmbeddedWebApplicationContext并提供servlet环境，内嵌服务的监听端口是随机的
        3. DEFINED_PORT —— 加载EmbeddedWebApplicationContext并提供servlet环境，内容服务的监听端口是定义好的。默认端口是8080
        4. NONE —— 加载ApplicationContext，使用SpringApplication但不提供任何Servlet环境

  - @RunWith(SpringRunner.class)
    
    运行Junit并支持Spring-test特性

  - @MockBean

    声明需要模拟的服务

  - @SpyBean 

    定制化需要模拟服务的某个方法，即部分方法可以mock，部分方法可以调用真实方法

  - @WebMvcTest

    自动加载Spring MVC配置、MockMvc配置、并扫描注解类;
    
    在没有`@WebMvcTest`注解时，使用`@AutoConfiureMockMvc`可以自动配置MockMvc;

  - @Transaction

    开启事务，回滚测试方法对数据库的改变

### Detecting test configuration

如果您熟悉Spring Test Framework,那么您可能习惯于使用`@ContextConfiguration(classes=...)`来指定`@Configuration`要加载哪个Spring。

在测试Spring Boot应用程序时，通常不需要这么做。Spring Boot的`@**Test`注解会在您没有明确定义主要配置时自动搜索您的主要配置文件。

搜索算法从包含测试的软件包开始工作，直到找到`@SpringBootApplication`或`@SpringBootConfiguration`注释的类。只要你以合理的方式构建你的代码，通常会找到你的主要配置。

如果你想定制主配置，可以使用`@TestConfiguration`类。与`@Configuration`将使用嵌套类而不是应用程序的主配置不同，`@TestConfiguration`除了应用程序的主配置之外，还将使用嵌套类。

### Working with random ports

如果你需要启动完整的运行服务器进行测试，我们建议您使用随机端口。

如果您使用`@SpringBootTest(webEnvironment=WebEnvironment.RANDOM_PORT)`，则每次运行测试时会随机选取可用端口。

`@LocalServerPort`注解，获取测试时动态的端口号。为了方便起见，各可以`@Autowire`注解一个`TestRestTemplate`，将解析到正在运行的服务器。

### Mocking and spying 

`@MockBean`,可为`ApplicationContext`内部的Bean定义Mockito Mock。

### Auto-configured tests

`spring-boot-test-autoconfigure`模块包含许多可用于自动配置.

----

## 打桩


