#SpringBoot实战

## 第一章 入门

- Spring Boot简化Spring应用程序开发
- Spring Boot的基本特性
- Spring Boot工作区的设置

1. 过去：繁琐的配置、依赖管理(版本冲突)

2. Spring Boot精要：
- 自动配置

  如果Spring Boot在应用程序的classpath里发现`JdbcTemplate`，那么它会为你配置一个`JdbcTemplate`的Bean。
- 起步依赖

  org.springframeword.boot:spring-boot-starter-web，会根据依赖传递把其他所需依赖引入项目里。

  Spring Boot起步依赖基本都以spinrg-boot-starter打头，随后是直接代表其功能的名字，比如web,test。
- 命令行界面
- Actuator

  提供在运行时检视应用程序内部情况的能力。

3.Spring Boot在应用程序里嵌入了一个Servlet容器(Tomcat、Jetty或Undertow)，因此Spring Boot可以把Web应用程序变为可自执行的JAR文件，不用部署到传统Java应用服务器里就能在命令行里运行。

## 第二章 开发第一个应用程序

- 使用起步依赖
- 使用自动配置

1. `@SpringBootApplication`开启了Spring的组件扫描和Spring Boot的自动配置功能。实际上，`@SpringBootApplication`将三个有用的注解组合在了一起。

    - Spring的`@Configuration`：标明该类使用Spring基于Java的配置。
    - Spring的`@ComponentScan`：启动组件扫描，这样你写的Web控制器类和其他组件才能被自动发现并注册为Spring应用上下文里的Bean。
    - Spring Boot的`@EnableAutoConfiguration`：开启Spring Boot自动配置。

2. 配置类  条件化配置

  条件化注解 |  配置生效条件
  ---| ---
  @ConditionalOnBean | 配置了某个特定Bean
  @ConditionalOnMissingBean | 没有配置特定的Bean
  @ConditionalOnClass | classpath里有指定的类
  @ConditionalOnMissingClass | classpath里缺少指定的类
  @ConditionalOnExpression | 给定的Spring Expression Language(SpEL)表达式计算结果为ture
  @ConditionalOnJava | Java的版本匹配特定值或者一个范围值
  @ConditionalOnJndi | 参数中给定的JNDI位置必须存在一个，如果没有给参数，则要有JNDI InitialContext
  @ConditionalOnProperty | 指定的配置属性要有一个明确的值
  @ConditionalOnResourse | classpath里有指定的资源
  @ConditionalOnWebApplication | 这是一个Web应用程序
  @ConditionalOnNotWebApplication | 这不是一个Web应用程序

## 第三章 自定义配置

- 覆盖自动配置的Bean
- 用外置属性进行配置
- 自定义错误页

1. 想要覆盖Spring Boot的自动配置，仅仅需要编写一个显式的配置。Spring Boot会发现你的配置，随后降低自动配置的优先级，以你的配置为准。

2. Spring Boot的设计是加载应用级配置，随后再考虑自动配置类。

3. application.yml里的属性会覆盖application.properties里的属性

4. 配置日志：默认情况下，Spring Boot会用Logback来记录日志，并用INFO级别输出到控制台。

   用其他日志实现替换Logback：
   一般来说，不需要切换日志实现。但如果决定使用Log4j或Log4j2，那么你只需要修改依赖，引入对应该日志实现的起步依赖，同时排除掉Logback：

   ```pom
   <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
    <exclusions>
      <exclusion> 
        <groupId>org.springframework.boot</groupId>     <artifactId>spring-boot-starter-logging</artifactId>
      </exclusion>
    </exclusions>
  </dependency>

  <!--添加Log4J-->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-log4j</artifactId>
  </dependency>

  <!--添加Log4J2-->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-log4j2</artifactId>
  </dependency>
   ```

  要完全掌握日志配置，可以在classpath的根目录(src/main/resources)里创建logback.xml文件：

  ```xml
  <configuration>
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <encoder> 3 <pattern>
        %d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n
      </pattern>
    </encoder>
    </appender>
    <logger name="root" level="INFO"/>
    <root level="INFO">
      <appender-ref ref="STDOUT" />
    </root>
  </configuration>
  ```

  设置日志级别，可以创建logging.level开头的属性。
  如果根 日志级别要设置为WARN，但Spring Security的日志要用DEBUG级别，可以在application.yml里加入 6 以下内容:

  ```yml
  logging:
    config: 
      classpath: logging-config.xml
    level:
      root: WARN
      org:
        springframework:
          security: DEBUG
  ```

5. Spring Boot的属性解析器非常智能，它会自动把驼峰规则的属性和使用连字符或下划线的同名属性关联起来。例如，amazon.associateId这个属性和amazon.associate_id以及amazon.associate-id都是等价的。

6. 使用Profile进行配置，来设置特定环境的配置。

   ```pom
   spring:
      profiles:
        active: production
   ```

   (1). 可以使用application-{profile}.yml/.properties的方式
   (2). 在YAML文件中可以把所有Profile的配置属性都放在一个application.yml文件里。使用一组三个连字符(---)作为分隔符。未指定spring.profiles，则这里的属性对全部Profile都生效，或者对那些未设置该属性的激活Profile生效。
   











相关：
依赖注入
面向切面编程
Spring Framework
Spring4.0条件化配置