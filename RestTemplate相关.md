### RestTemplate 相关

> 简化了发起HTTP请求以及处理响应的过程，并且支持REST。



#### Exchange()

- 允许调用者指定HTTP请求的方法(GET,POST,PUT,DELETE等)

- 可以在请求中增加body以及头信息，其内容通过参数**HttpEntity<?> requestEntity** 描述

- exchange支持**含参数的类型(即泛型)** 作为返回类型，该特性通过 **ParameterizedTypeReference<T> responseType** 描述，比如：

  ```java
  List<String> a = new ArrayList<String>();
  System.out.println(a.getClass());		//java.util.ArrayList
  System.out.println(a.getClass().getGenericSuperClass());	//java.util.AbstractList<E>
  ParameterizedTypeReference<ArrayList<String>> pt = new ParameterizedTypeReference<ArrayList<String>>(){};	//java.util.ArrayList<java.lang.String>
  System.out.println(pt.getType())
  ```

  ​

#### Excute()

所有的get,post,deleted,put,option,head,exchange方法最终调用的都是excute()



#### HttpMessageConverter

RestTemplate通过HttpMessageConverter自动帮我们做了转换。

默认情况下，ResTemplate自动帮我们注册了一组HttpMessageConverter用来处理一些不同的`contentType`的请求。如StringHttpMessageConverter来处理text/plain;MappingJackson2HttpMessageConverter来处理application/json;MappingJackson2XmlHttpMessageConverter来处理application/xml。

如果现有的转换器不能满足需求，可以实现`org.springframework.http.converter.HttpMessageConverter接口自己写一个。



####  CloseableHttpClient

```java
@Bean
public RestTemplate companyClassifyRestTemplate(){
  CloseableHttpClient httpClient = HttpClientBuilder.create()
    .setRetryHandler(new DefaultHttpRequestRetryHandler(5, true))
    .setMaxConnPerRoute(16)
    .setMaxConnTotal(16)
    .disableCookieManagement()
    .build();
  return new RestTemplate(new HttpComponentsClientHttpRequestFactory(httpClient));
}
```





*附录*:

[restTemplate使用实例](https://segmentfault.com/a/1190000007778403)