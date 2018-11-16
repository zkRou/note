---
title: 《云原生Java》笔记
author: Kairou Zeng
date: 2018/10/23
tags: [Java, 读书笔记]
---

## 第1章 云原生应用程序

1. 构建微服务的主要思想之一：能够让开发团队根据指定的业务能力来组织自己。

2. 十二要素 - 应用程序开发理论

    `十二要素程序的核心思想:`

    - 使用`声明`的方式来搭建自动化环境，最大限度地减少新加入项目的开发人员的时间和成本

    - 与底层操作系统之间建立清晰的约定，在执行环境之间提供`最大的可移植性`

    - 适合`部署`在现代的云`平台`上，无须提供服务器和系统管理工具

    - `最大程度地减少`开发环境与生产环境之间的区别，通过`持续部署`获得最大的灵活性

    - 可以在不对工具、架构或开发实践带来重大变动的前提下，进行`水平扩展`

    `十二要素程序的实践:`

    - 代码库 - 一份版本控制下的基准代码库，多份部署

        应用程序的源代码仓库应该只包含一个应用程序，并列出它所依赖的资源清单。对于不同的环境，我们应该不需要重新编译或打包应用程序。

    - 依赖 - 显式声明和隔离依赖关系

    - 配置 - 在环境中存储配置

        应用程序的配置应由各自环境管理。

    - 后端服务 - 把后端服务当作附加资源

        后端服务是十二要素程序正常运行所需要依赖的任何服务（即包括数据库、API驱动的RESTful Web服务、SMTP服务器或FTP服务器）

    - 构建、发布、运行 - 严格分离构建和运行阶段

        `构建阶段`：构建阶段将应用程序的源代码编译或打包到一个程序包中。创建的包被称为一次构建物。

        `发布阶段`：发布阶段需要将某次构建与其配置配置相结合。随后，创建出的发布文件应该可以在某个执行环境中运行。无论是使用版本号还是时间戳，每个版本应该有一个唯一的标识符。每个发布文件都应该被添加到一个目录中，可以通过发布管理工具回滚到之前的发布版本。

        `运行阶段`：指在可执行环境中运行一个指定的应用版本。

    - 进程 - 将应用程序作为一个或多个无状态进程执行

    - 端口绑定- 通过端口绑定暴露服务

    - 并发 - 通过进程模型进行扩展

    - 易处理 - 通过快速启动和正常关机来最大限度地提高健壮性

    - 开发/生产环境一致性 - 尽可能保持开发、预发布和生产环境的配置一致

    - 日志 - 将日志视为事件流

    - 管理进程 - 将管理任务作为一次性进程进行

## 第2章 训练营：Spring Boot 和 Cloud Foundry

1. `@ComponentScan`: 这个注解告诉Spring扫描当前的包（或下级的包）并查找所有使用`@Component`等原型注解的对象，然后把这些bean注册到应用程序上下文中。这个注解和其他注解也都被标记了`@Component`注解,它就像标签一样被Spring作为一种记号。Spring会感知到它们标记在某些组件上，并为它们创建一个新的对象实例。它默认调用无参数的构造函数，或者当它的所有参数在应用程序上下文都有对应的引用时，它才会调用含有参数的构造函数。

2. 声明式事务管理

3. `JdbcTemplateAutoConfiguration.class`

    ```java
    @Configuration
    //只有类型为DataSource.class和JdbcTemplate.class的类在路径中时，才会被认为是配置类；否则一定会失败并抛出类似ClassNotFoundException的错误
    @ConditionalOnClass({ DataSource.class, JdbcTemplate.class })
    //如果在应用程序上下文中已经引入了一个DataSource bean，我们希望只使用这个配置类
    @ConditionalOnSingleCandidate(DataSource.class)
    @AutoConfigureAfter(DataSourceAutoConfiguration.class)
    public class JdbcTemplateAutoConfiguration {

        private final DataSource dataSource;

        public JdbcTemplateAutoConfiguration(DataSource dataSource) {
            this.dataSource = dataSource;
        }

        @Bean
        @Primary
        @ConditionalOnMissingBean(JdbcOperations.class)
        public JdbcTemplate jdbcTemplate() {
            return new JdbcTemplate(this.dataSource);
        }

        @Bean
        @Primary
        //希望引入一个NamedParameterJdbcTemplate,但只有当它不存在的时候才可以
        @ConditionalOnMissingBean(NamedParameterJdbcOperations.class)
        public NamedParameterJdbcTemplate namedParameterJdbcTemplate() {
            return new NamedParameterJdbcTemplate(this.dataSource);
        }

    }
    ```

    `@ConditionalOnClass`：这个注解的参数对应的类必须存在，否则不解析该注解修饰的配置类。

    `@ConditionalOnSingleCandidate`：当指定Bean在容器中只有一个，或者虽然有多个但是指定首选Bean

    `@AutoConfigureAfter`：在指定的配置类初始化后再加载 

    `@ConditionalOnMissingBean`：当容器里没有指定Bean的情况下

4. Spring Boot - 把重点放在业务逻辑上，大大降低实际需要编写的代码量以及重复工作量，而由框架来实现细节。

5. Cloud Foundry - 一个平台即服务（Pass）的模式。

6. Reactor项目 - 目标是避免资源的过度使用，并有效隔离使用云计算时的阻塞工作。为Cloud Foundry Java API出色的效率提供了基础。

## 第3章 符合十二要素程序风格的配置

1. 消除魔法字符串(在程序代码中硬编码的数据库地址、密码、端口等值)。将这些配置外部化，那么不需要重新编译代码就可以更换配置。

