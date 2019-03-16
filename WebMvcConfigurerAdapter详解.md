---
title: WebMvcConfigurerAdapter
author: Kairou Zeng
date: 2018/04/14
tags: [web]
---

### WebMvcConfigurerAdapter

> WebMvcConfigurerAdapter是WebMvcCongifurer接口的实现。因其是WebMvcConfigurer接口的实现，所以WebMvcConfigurer的API方法也可以用来配置MVC。只是实现这个接口的话，要实现所有的方法。Spring API中对该类的描述如下：
>
> An implementation of [`WebMvcConfigurer`](http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/servlet/config/annotation/WebMvcConfigurer.html) with empty methods allowing subclasses to override only the methods they're interested in.
>
> 通常情况下，Spring Boot的自动配置是符合我们大多数需求的。在你既需要保留Spring boot提供的便利，又需要增加自己的额外配置时候，可以定义一个配置类并继承WebMvcConfigurerAdapter，重写类中的方法可以让我们增加额外的配置。

- 自定义资源映射addResourceHandlers

  ```java
  @Configuration
  public class WebMvcConfig extends WebMvcConfigurerAdapter{
      
    @Override
      public void addResourceHandlers(ResourceHandlerRegistry registry) {
          registry.addResourceHandler("/my/**").addResourceLocations("classpath:/my/");
        	//当指定外部目录时
          registry.addResourceHandler("/my/**").addResourceLocations("file:E:/my/");
          super.addResourceHandlers(registry);
      }
  }
  ```

  addResourceLocations指的是文件放置的目录

  addResoureHandler指的是对外暴露的访问路径

- 页面跳转addViewControllers

  可以很方便的实现一个请求到视图的映射，而无需书写controller。

  ```java
  	/**
       * 以前要访问一个页面需要先创建个Controller控制类，再写方法跳转到页面
       * 在这里配置后就不需要那么麻烦了，直接访问http://localhost:8080/toLogin就跳转到login.jsp页面
       * @param registry
       */
      @Override
      public void addViewControllers(ViewControllerRegistry registry) {
          registry.addViewController("/toLogin").setViewName("login");
          super.addViewControllers(registry);
      }
  ```

  在这里重写addViewControllers方法，并不会覆盖WebMvcAutoConfiguration中的addViewControllers（在此方法中，Spring Boot将“/”映射至index.html），这也就意味着我们自己的配置和Spring Boot的自动配置同时有效。

- 拦截器addIntereceptors

  实现拦截器：

  1. 实现自己的拦截器类病继承HandlerInterceptorAdapter类（可参看HandlerInterceptorAdapter详解）
  2. 重写WebMvcConfigureAdapter中的addInterceptors方法把自定义的拦截器类添加进去 

- addReturnValueHandlers

  Add handlers to support custom controller method return value types.

  ```java
  @Override
  public void addReturnValueHandlers(List<HandlerMethodReturnValueHandler> returnValueHandlers) {
          ObjectMapper objectMapper = mappingJackson2HttpMessageConverter.getObjectMapper();
          objectMapper.disable(SerializationFeature.FAIL_ON_EMPTY_BEANS);
          returnValueHandlers.add(new JsonResultMethodProcessor(Collections.singletonList(mappingJackson2HttpMessageConverter)));
      }
  ```

  实现自定义的转换器时。

  