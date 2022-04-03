

# Feign



## 概览



1. feign 是一个 web 服务库，让编写 web 服务客户端变得简单
   - 只需要：1、编写一个接口，2、标注上注解
2. 支持 feign 注解和 JAX-RS 注解
3. 支持可插拔的编码器和解码器（encoder、decoder）
4. 支持 spring MVC 注解
5. 支持负载均衡（Ribbo LoadBalancer, Spring Cloud LoadBalancer）、断路器（CircuitBreaker）
   - 禁用 Ribbon LB：`spring.cloud.loadbalancer.ribbon.enabled=false`



## 引入 Feign

引入 stater 即可

```xmk
 <!--openFeign-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
```



例子：

启动类

```java
@SpringBootApplication
@EnableFeignClients
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

}
```



`StoreClient.java`

```java
@FeignClient("stores")
public interface StoreClient {
    @RequestMapping(method = RequestMethod.GET, value = "/stores")
    List<Store> getStores();

    @RequestMapping(method = RequestMethod.GET, value = "/stores")
    Page<Store> getStores(Pageable pageable);

    @RequestMapping(method = RequestMethod.POST, value = "/stores/{storeId}", consumes = "application/json")
    Store update(@PathVariable("storeId") Long storeId, Store store);
}
```



- @Feign
  - value 属性：
  - url 属性：
  - qualifiers 属性：
    - 没有这个属性时，在 spring IOC 容器种名字是接口的全限定名
    - 可以使用这个注解指定 bean 属性

- 如何解析 'stores' 服务的物理地址呢？
  - 通过注册中心（Eureka、Nacos)
  - 通过外部化配置（`SimpleDiscoveryClient`)



## 重写默认配置



1. 默认配置在哪？

   - `FeignClientsConfiguration`

2. Feign 的配置包括什么？

   - `Decoder`, `Encoder`, `Contract`

3. 如何添加自己的配置?

   使用 configuration 属性

   ```java
   @FeignClient(name = "stores", configuration = FooConfiguration.class)
   public interface StoreClient {
       //..
   }
   ```

4. 关于自定义配置类有哪些需要注意的地方

   - 不要被 spring IOC 容器扫描到

5. 自定义配置 和 `FeignClientsConfiguration` 配置优先级

   - 自定义配置会覆盖`FeignClientsConfiguration`的相同的组件

6. 默认配置的组件，以 bean 形式

   ![image-20220329001312656](https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220329001312.png)







## 超时处理



- 两种超时配置

  1. connectTimeout
     - 建立连接的过程慢
     - 包括：DNS 解析、发包、收包
  2. readTimeout
     - 连接已经建立，但是响应慢

- 当使用 hystrix 时，并且启用了 hystrix timeout

  hytrix 的默认超时是 1000 ms，通常需要增加值

  ```yml
  feign:
    client:
      config:
        default:
          connectTimeout: 5000
          readTimeout: 5000
    hystrix:
      enabled: true
  
  hystrix:
    command:
      default:
        execution:
          timeout:
            enabled: true
          isolation:
            thread:
              timeoutInMilliseconds: 60000
  ```





## 手动创建 Feign client - `FeignClientBuilder`



跟使用 @Feignclient 注解一样，可以创建 Feign client



```java
@Import(FeignClientsConfiguration.class)
class FooController {

    private FooClient fooClient;

    private FooClient adminClient;

        @Autowired
    public FooController(Decoder decoder, Encoder encoder, Client client, Contract contract) {
        this.fooClient = Feign.builder().client(client)
                .encoder(encoder)
                .decoder(decoder)
                .contract(contract)
                .requestInterceptor(new BasicAuthRequestInterceptor("user", "user"))
                .target(FooClient.class, "https://PROD-SVC");

        this.adminClient = Feign.builder().client(client)
                .encoder(encoder)
                .decoder(decoder)
                .contract(contract)
                .requestInterceptor(new BasicAuthRequestInterceptor("admin", "admin"))
                .target(FooClient.class, "https://PROD-SVC");
    }
}
```



## Hystrix 支持



- 如何使用 Hystrix
  1. 将 hystrix 的包放到 classpath 下
  2. 设置：`feign.hystrix.enabled=true`
- hystrix 做了什么?
  - 将 feign client 中的每个方法包装成了 `circuit breaker`





## Hystrix Fallback



- 什么是 hystrix fallback？

  - 一种执行方式。当 circut 打开或者 feign client 报错的时候，执行

- 如何使用 fallback？

  - @Feign 的 fallback 属性
  - 【注意需要，将 fallback 的类声明为一个 spring bean】

  ```java
  @FeignClient(name = "hello", fallback = HystrixClientFallback.class)
  protected interface HystrixClient {
      @RequestMapping(method = RequestMethod.GET, value = "/hello")
      Hello iFailSometimes();
  }
  
  @Component
  static class HystrixClientFallback implements HystrixClient {
      @Override
      public Hello iFailSometimes() {
          return new Hello("fallback");
      }
  }
  ```

- 如何知道错误原因？

  - 使用@Feign 的 fallbackFactory属性

  ```java
  @FeignClient(name = "hello", fallbackFactory = HystrixClientFallbackFactory.class)
  protected interface HystrixClient {
      @RequestMapping(method = RequestMethod.GET, value = "/hello")
      Hello iFailSometimes();
  }
  
  @Component
  static class HystrixClientFallbackFactory implements FallbackFactory<HystrixClient> {
      @Override
      public HystrixClient create(Throwable cause) {
          return new HystrixClient() {
              @Override
              public Hello iFailSometimes() {
                  return new Hello("fallback; reason was: " + cause.getMessage());
              }
          };
      }
  }
  ```



## Feign 短路器支持



- 如何在 Feign client 中使用`circuitbreaker`?

  1. 将 spring cloud CircuitBreaker 包放到类路径下
  2. feign.circuitbreaker.enabled=true

- 启用这个功能时，会做什么？

  - Feign client 中所有方法，都会用 `circuit breaker` 包裹

- 如何让一个 feign client 不支持`circuitbreaker`

  ```java
  @Configuration
  public class FooConfiguration {
          @Bean
      @Scope("prototype")
      public Feign.Builder feignBuilder() {
          return Feign.builder();
      }
  }
  ```

- `circuitbreaker` 名字是什么？

  - <feignClientName>_<calledMethod>



## Feign 和 @Primay

- 背景

  - 当启用 fallback 时，如果在 IOC 容器中有多个相同的 fallback bean，spring 的 @Autowired 就会失效

- 解决办法

  - fallback bean 使用 @Pirmary 注解

  - @FeignClient 中将 primary 设置为 false

    ```java
    @FeignClient(name = "hello", primary = false)
    public interface HelloClient {
        // methods here
    }
    ```



## Feign client 可以继承

继承：将通用的操作，放在基类上



`UserService`

```java
public interface UserService {

    @RequestMapping(method = RequestMethod.GET, value ="/users/{id}")
    User getUser(@PathVariable("id") long id);
}
```



**UserClient.java**

```java
@FeignClient("users")
public interface UserClient extends UserService {

}
```





## Feign 请求和响应压缩



You may consider enabling the request or response GZIP compression for your Feign requests. You can do this by enabling one of the properties:

```prop
feign.compression.request.enabled=true
feign.compression.response.enabled=true
```



- [ ] todo





## Feign 日志



- 每一个 feign client 都有一个 logger

- 默认的 logger 名称是 feign 接口的全类名

- 默认 logger 只记录 debug 级别的日志（**待确定**）

- 配置每个 feign client 可以使用 `Logger.Level` 对象

  - `NONE`, No logging (**DEFAULT**).
  - `BASIC`, Log only the request method and URL and the response status code and execution time.
  - `HEADERS`, Log the basic information along with request and response headers.
  - `FULL`, Log the headers, body, and metadata for both requests and responses.

  比如，配置 `Full`

  ```java
  @Configuration
  public class FooConfiguration {
      @Bean
      Logger.Level feignLoggerLevel() {
          return Logger.Level.FULL;
      }
  }
  ```



- 异常的时候、请求过程都会记录日志

  ```java
  @Override
    public Object invoke(Object[] argv) throws Throwable {
      RequestTemplate template = buildTemplateFromArgs.create(argv);
      Options options = findOptions(argv);
      Retryer retryer = this.retryer.clone();
      while (true) {
        try {
          return executeAndDecode(template, options);
        } catch (RetryableException e) {
          try {
            retryer.continueOrPropagate(e);
          } catch (RetryableException th) {
            Throwable cause = th.getCause();
            if (propagationPolicy == UNWRAP && cause != null) {
              throw cause;
            } else {
              throw th;
            }
          }
            // 异常，则记录日志
          if (logLevel != Logger.Level.NONE) {
            logger.logRetry(metadata.configKey(), logLevel);
          }
          continue;
        }
      }
    }
  ```

  



## @QueryMap



- @QueryMap 是 Feign 提供的，但是不能与spring兼容
- spring 提供了 `@SpringQueryMap`
- get 请求，有多个参数时，也可以用 POJO 传参，前提是使用 @SpringQueryMap 注解



比如，params 类中定义两个参数 param1，param2

```java
public class Params {
    private String param1;
    private String param2;

    // [Getters and setters omitted for brevity]
}
```



get 请求使用这两个参数

```java
@FeignClient("demo")
public interface DemoTemplate {

    @GetMapping(path = "/demo")
    String demoEndpoint(@SpringQueryMap Params params);
}
```



## HATEOAS support



- 如果你的项目中引入了，下面任意一个 starter ，这个支持就是开启的

  - org.springframework.boot:spring-boot-starter-hateoas
  - org.springframework.boot:spring-boot-starter-data-rest

- 如果 hateos 被支持了，feign client 是被允许序列化和反序列化hateos相关的模型（[EntityModel](https://docs.spring.io/spring-hateoas/docs/1.0.0.M1/apidocs/org/springframework/hateoas/EntityModel.html), [CollectionModel](https://docs.spring.io/spring-hateoas/docs/1.0.0.M1/apidocs/org/springframework/hateoas/CollectionModel.html) and [PagedModel](https://docs.spring.io/spring-hateoas/docs/1.0.0.M1/apidocs/org/springframework/hateoas/PagedModel.html)）

  ```java
  @FeignClient("demo")
  public interface DemoTemplate {
  
      @GetMapping(path = "/stores")
      CollectionModel<Store> getStores();
  }
  ```





## @MatrixVariable 支持









## Reactive 支持



- 有的版本支持，有的不支持
- spring 建议使用



# 可配置属性



- 官网：https://docs.spring.io/spring-cloud-openfeign/docs/2.2.10.RELEASE/reference/html/appendix.html





# 源码分析



- 如何分析？

  回想一下，我们在springboot 项目中使用 feign 的步骤：

  1. 引入`spring-cloud-starter-openfeign`
  2. 启动类上标注 @EnableFeignClients
  3. 编写接口，并标注 @FeignClient

  所以，分析步骤可以按照这样顺序：

  1. 查看 stater-openfeign 提供的自动配置
  2. 分析@EnableFeignClients
  3. 分析 @FeignClient



## 自动配置



项目：`spring-cloud-starter-openfeign` 的子模块 `spring-cloud-openfeign-core` 的`spring.facotories` 中内容：

```properties
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.springframework.cloud.openfeign.ribbon.FeignRibbonClientAutoConfiguration,\
org.springframework.cloud.openfeign.hateoas.FeignHalAutoConfiguration,\
org.springframework.cloud.openfeign.FeignAutoConfiguration,\
org.springframework.cloud.openfeign.encoding.FeignAcceptGzipEncodingAutoConfiguration,\
org.springframework.cloud.openfeign.encoding.FeignContentGzipEncodingAutoConfiguration,\
org.springframework.cloud.openfeign.loadbalancer.FeignLoadBalancerAutoConfiguration

```



### `FeignRibbonClientAutoConfiguration`



- 启用条件：

  1. 项目有这两个类：

     1. `com.netflix.loadbalancer.ILoadBalancer`, 
     2. `feign.Feign`

  2. `spring.cloud.loadbalancer.ribbon.enabled=true`

  3. 满足上面两个条件，需要引入两个 starter

     ```xml
      <!--openFeign-->
             <dependency>
                 <groupId>org.springframework.cloud</groupId>
                 <artifactId>spring-cloud-starter-openfeign</artifactId>
             </dependency>
     
             <dependency>
                 <groupId>org.springframework.cloud</groupId>
                 <artifactId>spring-cloud-starter-loadbalancer</artifactId>
             </dependency>
     ```

     

- 作用：
  - 让 Feign 有负载均衡功能（还是使用的 ribbon）
- 生成的 client 都是：`org.springframework.cloud.openfeign.ribbon.LoadBalancerFeignClient`
  - 这些 client 都是由 `FeignRibbonClientAutoConfiguration` 导入的配置类生成
    - `HttpClientFeignLoadBalancedConfiguration`
    - `OkHttpFeignLoadBalancedConfiguration`
    - `HttpClient5FeignLoadBalancedConfiguration`
    - `DefaultFeignLoadBalancedConfiguration`

- `LoadBalancerFeignClient`
  - 内部组合一个 `Client` , 发送请求的都是委托给这个 `Client`



### `FeignHalAutoConfiguration`



- [ ] 一般不使用这个:100: ​先不分析





### `FeignAutoConfiguration`



- [ ] 这个目前分析不出什么



目前所知：

- 每个 client 配置，都是由这个控制

  <img src="https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220401195349.png" alt="image-20220401195349340" style="zoom: 50%;" />

  





### `FeignAcceptGzipEncodingAutoConfiguration`



- 启用条件
  - `feign.compression.response.enabled=true`
- 对应的 properties 配置：`FeignClientEncodingProperties`
  - 配置前缀：feign.compression.request
- 怎么做的？
  - 通过 `FeignAcceptGzipEncodingInterceptor` 添加了请求头：`Accept-Encoding: gzip, deflate`



### `FeignContentGzipEncodingAutoConfiguration`



**基本上和 3.1.4 一样**





### `FeignLoadBalancerAutoConfiguration`

- 作用
  - 也是让 feign client 有负载均衡功能
- 特点
  - 提供的 client 是 `BlockingLoadBalancerClient` 的子类

- 启用条件
  - 类 `BlockingLoadBalancerClient`存在
- 对应的 properties 配置类，`FeignHttpClientProperties`
- 导入的配置类
  - `HttpClientFeignLoadBalancerConfiguration`
    - 注入 ioc 容器的 client ：`FeignBlockingLoadBalancerClient`
  - `OkHttpFeignLoadBalancerConfiguration`
    - 注入 ioc 容器的 client ：`FeignBlockingLoadBalancerClient`
  - `HttpClient5FeignLoadBalancerConfiguration`
    - 注入 ioc 容器的 client ：`FeignBlockingLoadBalancerClient`
  - `DefaultFeignLoadBalancerConfiguration`
    - 注入 ioc 容器的 client ：`FeignBlockingLoadBalancerClient`
- 特点，注入 ioc 容器的 client 类型都是：`org.springframework.cloud.openfeign.loadbalancer.FeignBlockingLoadBalancerClient`







## @Import



- 作用：向 IOC 容器导入 spring bean

- 可以导入多个，也可以导入一个

- bean 在 IOC 容器中的名字是全限定类名

- 可以导入什么？

  1. 常规类（regular comonent class)
  2. @Configuration 标注的类
  3. `ImportSelector`: 返回需要导入的组件的全类名
  4.  `ImportBeanDefinitionRegistrar`

  





- 将第三方包的 class 导入到 spring IOC 容器中方法：

  - @Import
  - @Bean

  

  

## `@EnableFeignClients`

- 注解属性
  - 开发中，一般都是直接使用，没有配置任何属性
  - `Class<?>[] defaultConfiguration() default {};`，如果配置这个属性，会处理这个属性
  - `Class<?>[] clients() default {};`
    - 这些类，必须标注@FeignClient 
    - 如果不为空，则仅将这些类导入到 IOC 容器
    - 如果为空，则会进行包扫描，扫描 标注@FeignClient 的类
      - 包扫描器：`ClassPathScanningCandidateComponentProvider`
      - 扫描哪些类？标注 `@FeignClient` 的类
      - 扫描哪些包？
        - `String[] basePackages() default {};`
        - `Class<?>[] basePackageClasses() default {};`
        - `String[] value() default {};`
        - 或者 @Import 标注的类所在的包



### `FeignClientsRegistrar`



- 是什么？
  - `ImportBeanDefinitionRegistrar` 的实现类，注册 `BeanDefinition`

- 作用
  - 将 `@FeignClient `标注的类注册到 IOC 容器中。（通过包扫描，或者`@EnableFeignClients` 的 `Clients`属性）



## `@FeignClient`



- 就是一个普通注解

- doc 内容：

  Annotation for interfaces declaring that a REST client with that interface should be created (e.g. for autowiring into another component). If ribbon is available it will be used to load balance the backend requests, and the load balancer can be configured using a @RibbonClient with the same name (i.e. value) as the feign client.

  1. 如果一个接口标注了 @FeignClient，那么它是一个 RestClient
  2. 如果使用了 ribbon，那么 feign client 请求时，会负载均衡
  3. <u>*最后一句话，不理解*</u>



## 发送请求，获取响应 - `Client`



- 接口：`feign.Client`
- 重要 API
  -  `Response execute(Request request, Options options)`
- 所在项目：`io.github.openfeign:feign-core:10.12`



### 默认实现 - `Default`

`feign.Client.Default`

- 特点
  - 发送请求用到的都是 java 标注网络库`java.net`，或扩展库 `javax.net` 中的组件。比如：
    -  `java.net.HttpURLConnection`
    - `javax.net.ssl.HttpsURLConnection`



### 其它实现



![image-20220401192751267](https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220401192751.png)



| 实现                    | 项目                                       |
| ----------------------- | ------------------------------------------ |
| ApacheHttpClient        | io.github.openfeign.feign-httpclient:10.12 |
| OkHttpClient            | io.github.openfeign.feign-okhttpt:10.12    |
| ApacheHttp5Client       | io.github.openfeign.feign-hc5:10.12        |
| LoadBalancerFeignClient | spring.cloud.openfeign.core                |



其它实现，分布在不同的项目中，有 Feign 自己的其它实现，有 spring 自己的实现



### `URL` 组件



`java.net.URL`

- 重要API:
  - `URLConnection openConnection()`





### 网络连接 - `URLConnection`



`java.net.URLConnection`

- 重要 API:
  - 常规配置
    - `public void setConnectTimeout(int timeout)`
    - `public void setReadTimeout(int timeout)`
    - `public void setAllowUserInteraction(boolean allowuserinteraction)`
    - `public void setInstanceFollowRedirects(boolean followRedirects)`
    - `public void setRequestMethod(String method)`
    - `public void setDoOutput(boolean dooutput)`
  - 请求配置
    - `public void addRequestProperty(String key, String value)`
  - 获取要用 connection 输出的内容
    - `public OutputStream getOutputStream()`







### 子类 - `HttpURLConnection`



 `java.net.HttpURLConnection`

- 父类：`java.net.URLConnection`

- 子类：`javax.net.ssl.HttpsURLConnection`, **HTTPS**
- 重要 API:
  - 配置：
    - `public void setFixedLengthStreamingMode (int contentLength)`
    - `public void setChunkedStreamingMode (int chunklen)`
  - 获取
    - 获取响应码，`public int getResponseCode()`
    - 获取响应信息，`public String getResponseMessage()`

