<!-- TOC -->

- [1. 官方文档](#1-%E5%AE%98%E6%96%B9%E6%96%87%E6%A1%A3)
    - [1.1. overview](#11-overview)
    - [1.2. Spring Cloud Context](#12-spring-cloud-context)
        - [1.2.1. bootstrap context](#121-bootstrap-context)
        - [1.2.2. environment changes](#122-environment-changes)
        - [1.2.3. refresh scope](#123-refresh-scope)
    - [1.3. spring cloud commons](#13-spring-cloud-commons)
        - [1.3.1. common abstractions](#131-common-abstractions)
        - [1.3.2. @EnableDiscoveyClient](#132-enablediscoveyclient)
        - [1.3.3. ServiceRegistry](#133-serviceregistry)
            - [1.3.3.1. 自动注册](#1331-%E8%87%AA%E5%8A%A8%E6%B3%A8%E5%86%8C)
        - [1.3.4. RestTemplate 负载均衡](#134-resttemplate-%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1)
            - [1.3.4.1. Retrying Failed Requests](#1341-retrying-failed-requests)
        - [1.3.5. 忽略某些网络](#135-%E5%BF%BD%E7%95%A5%E6%9F%90%E4%BA%9B%E7%BD%91%E7%BB%9C)
        - [1.3.6. http client factories](#136-http-client-factories)
    - [1.4. spring cloud loadbalancer](#14-spring-cloud-loadbalancer)
        - [1.4.1. 切换 lb 算法](#141-%E5%88%87%E6%8D%A2-lb-%E7%AE%97%E6%B3%95)
        - [1.4.2. 缓存实现](#142-%E7%BC%93%E5%AD%98%E5%AE%9E%E7%8E%B0)
    - [1.5. spring cloud circuit breaker](#15-spring-cloud-circuit-breaker)

<!-- /TOC -->
# 1. 官方文档

`spring-cloud-commons` 、`spring-cloud-context` 版本：`2.2.9.RELEASE`



## 1.1. overview



- `spring-cloud-commons`。制定了 spring cloud application 的一些抽象，并提供了一些常用类
  - `DiscoveryClient` interface
  - `ServiceRegistry` interface
  - spring cloud 中的 `RestTemplate`
- `spring-cloud-context`。为 spring cloud application 的 `ApplicationContext` 提供了如下功能：
  - `Bootstrap Context`
  - `TextEncryptor` beans
  - Refresh Scope



## 1.2. Spring Cloud Context



### 1.2.1. bootstrap context

<img src="https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220827125835.png" alt="image-20220827125835456" style="zoom:50%;" />

```
疑问：
1. bootstrap context 和 main context 的区别是什么?
2. 
```





### 1.2.2. environment changes



配置中心对应的配置更改时，标注 `@ConfigurationProperties` 的 bean 会更改。此时，你的 spring cloud 应用会收到一个`EnvironmentChangeEvent` ，你可以实现一个`ApplicationListener` 去处理该 `event`



### 1.2.3. refresh scope

让标注有 `@Component` 的 spring bean 配置更改时，可以在这些 bean 的 class 上标注`@RefreshScope` 。怎么做到的呢？有一个 `refreshScope` 的 bean，该 spring bean 有个 `refreshAll()` 方法，这个方法会 `refresh` 这些 `bean`





## 1.3. spring cloud commons



### 1.3.1. common abstractions

该包提供了spring cloud 的基础抽象：

- 服务发现（service discovery）
- 复杂均衡（load balancing）
- 断路器（circuit breaker）





### 1.3.2. @EnableDiscoveyClient

Spring Cloud Commons provides the `@EnableDiscoveryClient` annotation. 该注解可以找到 classpath  `DiscoveryClient` 和 `ReactiveDiscoveryClient` 的实现





### 1.3.3. `ServiceRegistry`

```java
package org.springframework.cloud.client.serviceregistry;

public interface ServiceRegistry<R extends Registration> {

	/**
	 * Registers the registration. A registration typically has information about an
	 * instance, such as its hostname and port.
	 * @param registration registration meta data
	 */
	void register(R registration);

	/**
	 * Deregisters the registration.
	 * @param registration registration meta data
	 */
	void deregister(R registration);

	/**
	 * Closes the ServiceRegistry. This is a lifecycle method.
	 */
	void close();

	/**
	 * Sets the status of the registration. The status values are determined by the
	 * individual implementations.
	 * @param registration The registration to update.
	 * @param status The status to set.
	 * @see org.springframework.cloud.client.serviceregistry.endpoint.ServiceRegistryEndpoint
	 */
	void setStatus(R registration, String status);

	/**
	 * Gets the status of a particular registration.
	 * @param registration The registration to query.
	 * @param <T> The type of the status.
	 * @return The status of the registration.
	 * @see org.springframework.cloud.client.serviceregistry.endpoint.ServiceRegistryEndpoint
	 */
	<T> T getStatus(R registration);

}
```



很明显，该 bean 会将服务注册到注册中心。看下常见的实现：

![image-20220827134009735](https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220827134009.png)



想获取当前服务的状态，你可以这样（这里以常用的 `nacos` 为例）

```java
/**
 * @author liulonglong
 * @date 2022-08-27 13:49
 */
@Data
@RestController
@RequestMapping("serviceRegistry")
public class ServiceRegistry_controller {

    @Autowired
    ServiceRegistry serviceRegistry;
    @Autowired
    Registration registration;

    @GetMapping("queryStatus")
    public Result queryStatus() {
        Object status = serviceRegistry.getStatus(registration);
        return Result.ok(status);
    }

    @GetMapping("deregister")
    public Result deregister() {
        serviceRegistry.deregister(registration);
        return Result.ok(serviceRegistry.getStatus(registration));
    }

    @GetMapping("register")
    public Result register() {
        serviceRegistry.register(registration);
        return Result.ok(serviceRegistry.getStatus(registration));
    }
}

```



#### 1.3.3.1. 自动注册

默认情况下，`ServiceRegistry` 会将本服务自动注册。可以使用以下方式禁用自动注册：

1. `@EnableDiscoveryClient(autoRegister=false)`
2. `spring.cloud.service-registry.auto-registration.enabled=false`



另外，自动注册事件：

- 前后会有两个事件：`InstancePreRegisteredEvent`, `InstanceRegisteredEvent`。
- 可以实现对应的 `ApplicationListener` 处理。
- 当 `spring.cloud.service-registry.auto-registration.enabled` 时，上述两个事件不会触发



### 1.3.4. RestTemplate 负载均衡



**怎么让 `RestTemplate` 支持负载均衡？**

1. 在 `RestTemplate` 的 spring bean 上标注 `@LoadBalanced`
2. class path 下必须有 `org.springframework.cloud.client.loadbalancer.LoadBalancerClient` 实现

**使用 `RestTemplate` 时，需要注意什么？**

- 负载均衡方式使用时，uri 必须是服务名（service name)

**LoadBalancerClient**

- 两个实现：
  - `org.springframework.cloud.loadbalancer.blocking.client.BlockingLoadBalancerClient`
  - `org.springframework.cloud.netflix.ribbon.RibbonLoadBalancerClient`
- 如果，class path 下同时有上述两个实现，优先使用 `RibbonLoadBalancerClient` (spring cloud 文档说是为了兼容性)。可以设置 `spring.cloud.loadbalancer.ribbon.enabled=false` 强制使用 `BlockingLoadBalancerClient`





```java
// 使用
@Configuration
public class MyConfiguration {

    @LoadBalanced
    @Bean
    RestTemplate restTemplate() {
        return new RestTemplate();
    }
}

public class MyClass {
    @Autowired
    private RestTemplate restTemplate;

    public String doOtherStuff() {
        // stores: 服务名
        String results = restTemplate.getForObject("http://stores/stores", String.class);
        return results;
    }

```



#### 1.3.4.1. Retrying Failed Requests



- A load-balanced `RestTemplate` can be configured to retry failed requests
- 默认情况下，是不会 retry 失败的请求 



如何配置 RestTemplate 重试呢？

- 引入 spring-retry 即可

```xml
 <dependency>
            <groupId>org.springframework.retry</groupId>
            <artifactId>spring-retry</artifactId>
        </dependency>
```



如果，你引入了 spring-retry，又不想使用重试功能呢？

- 可以 `spring.cloud.loadbalancer.retry.enabled=false`





### 1.3.5. 忽略某些网络

```
- 为啥要有这个配置？忽略某些网络是干嘛啊？
```



>参考：https://docs.spring.io/spring-cloud-commons/docs/2.2.9.RELEASE/reference/html/#ignore-network-interfaces





### 1.3.6. http client factories



- `spring-cloud-common` 提供了两中 http client，`ApacheHttpClient` 和`OkHttpClient`
  - 有两个工厂：`ApacheHttpClientFactory`, `OkHttpClientFactory`
  - 两个连接管理器（connection manager）：`ApacheHttpClientConnectionManagerFactory`, `OkHttpClientConnectionPoolFactory`



这两个 http client 什么时候生效呢？可以看下：`org.springframework.cloud.commons.httpclient.HttpClientConfiguration` 配置类

```java
package org.springframework.cloud.commons.httpclient;

import okhttp3.OkHttpClient;
import org.apache.http.client.HttpClient;
import org.apache.http.impl.client.HttpClientBuilder;

import org.springframework.boot.autoconfigure.condition.ConditionalOnClass;
import org.springframework.boot.autoconfigure.condition.ConditionalOnMissingBean;
import org.springframework.boot.autoconfigure.condition.ConditionalOnProperty;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * @author Ryan Baxter
 */
@Configuration(proxyBeanMethods = false)
public class HttpClientConfiguration {

	@Configuration(proxyBeanMethods = false)
	@ConditionalOnProperty(name = "spring.cloud.httpclientfactories.apache.enabled",
			matchIfMissing = true)
	@ConditionalOnClass(HttpClient.class)
	static class ApacheHttpClientConfiguration {

		@Bean
		@ConditionalOnMissingBean
		public ApacheHttpClientConnectionManagerFactory connManFactory() {
			return new DefaultApacheHttpClientConnectionManagerFactory();
		}

		@Bean
		@ConditionalOnMissingBean
		public HttpClientBuilder apacheHttpClientBuilder() {
			return HttpClientBuilder.create();
		}

		@Bean
		@ConditionalOnMissingBean
		public ApacheHttpClientFactory apacheHttpClientFactory(
				HttpClientBuilder builder) {
			return new DefaultApacheHttpClientFactory(builder);
		}

	}

	@Configuration(proxyBeanMethods = false)
	@ConditionalOnProperty(name = "spring.cloud.httpclientfactories.ok.enabled",
			matchIfMissing = true)
	@ConditionalOnClass(OkHttpClient.class)
	static class OkHttpClientConfiguration {

		@Bean
		@ConditionalOnMissingBean
		public OkHttpClientConnectionPoolFactory connPoolFactory() {
			return new DefaultOkHttpClientConnectionPoolFactory();
		}

		@Bean
		@ConditionalOnMissingBean
		public OkHttpClient.Builder okHttpClientBuilder() {
			return new OkHttpClient.Builder();
		}

		@Bean
		@ConditionalOnMissingBean
		public OkHttpClientFactory okHttpClientFactory(OkHttpClient.Builder builder) {
			return new DefaultOkHttpClientFactory(builder);
		}

	}

}

```



一句话：class path 下有那个库，那个 http client 生效



## 1.4. spring cloud loadbalancer





### 1.4.1. 切换 lb 算法









### 1.4.2. 缓存实现



- `ServiceInstanceListSupplier` 





## 1.5. spring cloud circuit breaker



这一部分