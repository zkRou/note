---
title: Spring Security OAuth2.0
author: Kairou Zeng
date: 2018-08-23
tags: [OAuth2.0,SSO]
---

## 一些注解

- `@EnableOAuth2Sso`

    客户端的安全配置如下：

    ```java

    public class UiSecurityConfig extends WebSecurityConfigurerAdapter{

        @Override
        public void configure(HttpSecurity http) throws Exception{
            http.antMatcher("/**")
                .authorizeRequests()
                .antMatchers("/", "/login**").permitAll()
                .anyRequest()
                .authenticated();
        }
    }
    ```

    开启SSO。

    需要重写`WebSecurityConfigurerAdapter`，否则所有的路径都会收到SSO的保护，这样无论用户访问哪个页面都会被重定向到登录页面。

    在这个例子中，index和login页面是唯一不需要被防护的。
    
## 一些配置

 - `security.basic.enabled`: default Basic Authentication
 - `security.oauth2.client.access-token-uri`: 获取访问令牌的URL
 - `security.oauth2.client.user-authorization-uri`: 授权用户被重定向的目标URL
 - `security.oauth2.resource.user-info-uri`: 用户终端访问用户信息的URL