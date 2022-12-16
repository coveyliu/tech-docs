<!-- TOC -->

- [1. Using Spring Boot](#1-using-spring-boot)
    - [1.1. Build Systems](#11-build-systems)
        - [1.1.1. Dependency Management](#111-dependency-management)
        - [1.1.2. Starters](#112-starters)
- [2. Spring Boot Features](#2-spring-boot-features)
    - [2.1. Web Environment](#21-web-environment)
    - [2.2. Using the ApplicationRunner or CommandLineRunner](#22-using-the-applicationrunner-or-commandlinerunner)
- [3. Externalized Configuration](#3-externalized-configuration)
    - [3.1. Accessing Command Line Properties](#31-accessing-command-line-properties)
    - [3.2. Type-safe Configuration Properties](#32-type-safe-configuration-properties)
        - [3.2.1. JavaBean properties binding](#321-javabean-properties-binding)
        - [3.2.2. Enabling @ConfigurationProperties-annotated types](#322-enabling-configurationproperties-annotated-types)
        - [3.2.3. Third-party Configuration](#323-third-party-configuration)
- [4. Calling REST Services with RestTemplate](#4-calling-rest-services-with-resttemplate)
    - [4.1. RestTemplate Customization](#41-resttemplate-customization)
- [5. Task Execution and Scheduling](#5-task-execution-and-scheduling)

<!-- /TOC -->

# 1. Using Spring Boot

## 1.1. Build Systems

### 1.1.1. Dependency Management



### 1.1.2. Starters

- All **official** starters follow a similar naming pattern; `spring-boot-starter-*`, where `*` is a particular type of application
- a third-party starter typically starts with the name of the project. For example, a third-party starter project called `thirdpartyproject` would typically be named `thirdpartyproject-spring-boot-starter`.



| Name                                      | Description                                                  |
| :---------------------------------------- | :----------------------------------------------------------- |
| `spring-boot-starter`                     | Core starter, including auto-configuration support, logging and YAML |
| `spring-boot-starter-amqp`                | Starter for using Spring AMQP and Rabbit MQ                  |
| `spring-boot-starter-aop`                 | Starter for aspect-oriented programming with Spring AOP and AspectJ |
| `spring-boot-starter-cache`               | Starter for using Spring Framework’s caching support         |
| `spring-boot-starter-data-redis`          | Starter for using Redis key-value data store with Spring Data Redis and the Lettuce client |
| `spring-boot-starter-data-redis-reactive` | Starter for using Redis key-value data store with Spring Data Redis reactive and the Lettuce client |
| `spring-boot-starter-data-redis-reactive` | Starter for using Redis key-value data store with Spring Data Redis reactive and the Lettuce client |
| `spring-boot-starter-data-rest`           | Starter for exposing Spring Data repositories over REST using Spring Data REST |
| `spring-boot-starter-web`                 | Starter for building web, including RESTful, applications using Spring MVC. Uses Tomcat as the default embedded container |
| `spring-boot-starter-websocket`           | Starter for building WebSocket applications using Spring Framework’s WebSocket support |
|                                           |                                                              |
|                                           |                                                              |
|                                           |                                                              |





# 2. Spring Boot Features

## 2.1. Web Environment

A `SpringApplication` attempts to create the right type of `ApplicationContext` on your behalf. The algorithm used to determine a `WebApplicationType` is the following:

- If Spring MVC is present, an `AnnotationConfigServletWebServerApplicationContext` is used
- If Spring MVC is not present and Spring WebFlux is present, an `AnnotationConfigReactiveWebServerApplicationContext` is used
- Otherwise, `AnnotationConfigApplicationContext` is used

 It is often desirable to call `setWebApplicationType(WebApplicationType.NONE)` when using `SpringApplication` within a JUnit test.



## 2.2. Using the ApplicationRunner or CommandLineRunner

If you need to run some specific code once the `SpringApplication` has started, you can implement the `ApplicationRunner` or `CommandLineRunner` interfaces. Both interfaces work in the same way and offer a single `run` method, which is called just before `SpringApplication.run(…)` completes.

==This contract is well suited for tasks that should run after application startup but before it starts accepting traffic.==

The `CommandLineRunner` interfaces provides access to application arguments as a string array, whereas the `ApplicationRunner` uses the `ApplicationArguments` interface discussed earlier. The following example shows a `CommandLineRunner` with a `run` method:

```java
@Component
public class MyBean implements CommandLineRunner {

    public void run(String... args) {
        // Do something...
    }

}
```



If several `CommandLineRunner` or `ApplicationRunner` beans are defined that must be called in a specific order, you can additionally implement the `org.springframework.core.Ordered` interface or use the `org.springframework.core.annotation.Order` annotation.



# 3. Externalized Configuration

Spring Boot lets you externalize your configuration so that you can work with the same application code in different environments. You can use a variety of external configuration sources, include

- Java properties files, 
- YAML files,
- environment variables
- command-line arguments.

Property values can be injected directly into your beans by using the `@Value` annotation, accessed through Spring’s `Environment` abstraction, or be [bound to structured objects](https://docs.spring.io/spring-boot/docs/2.4.13/reference/html/spring-boot-features.html#boot-features-external-config-typesafe-configuration-properties) through `@ConfigurationProperties`.



## 3.1. Accessing Command Line Properties

- By default, `SpringApplication` converts any command line option arguments (that is, arguments starting with `--`, such as `--server.port=9000`) to a `property` and adds them to the Spring `Environment`.
-  As mentioned previously, command line properties always take precedence over file based property sources.
- If you do not want command line properties to be added to the `Environment`, you can disable them by using `SpringApplication.setAddCommandLineProperties(false)`.



## 3.2. Type-safe Configuration Properties

- Using the `@Value("${property}")` annotation to inject configuration properties can sometimes be cumbersome
- Spring Boot provides an alternative method of working with properties that lets strongly typed beans govern and validate the configuration of your application

### 3.2.1. JavaBean properties binding

It is possible to bind a bean declaring standard JavaBean properties as shown in the following example:

```java
@ConfigurationProperties("acme")
public class AcmeProperties {

    private boolean enabled;

    private InetAddress remoteAddress;

    private final Security security = new Security();

    public boolean isEnabled() { ... }

    public void setEnabled(boolean enabled) { ... }

    public InetAddress getRemoteAddress() { ... }

    public void setRemoteAddress(InetAddress remoteAddress) { ... }

    public Security getSecurity() { ... }

    public static class Security {

        private String username;

        private String password;

        private List<String> roles = new ArrayList<>(Collections.singleton("USER"));

        public String getUsername() { ... }

        public void setUsername(String username) { ... }

        public String getPassword() { ... }

        public void setPassword(String password) { ... }

        public List<String> getRoles() { ... }

        public void setRoles(List<String> roles) { ... }

    }
}
```

### 3.2.2. Enabling @ConfigurationProperties-annotated types

- Spring Boot provides infrastructure to bind `@ConfigurationProperties` types and register them as beans.
  - You can either enable configuration properties on a class-by-class basis
  - or enable configuration property scanning that works in a similar manner to component scanning.



specify the list of types to process using the `@EnableConfigurationProperties` annotation. This can be done on any `@Configuration` class, as shown in the following example:

```java
@Configuration(proxyBeanMethods = false)
@EnableConfigurationProperties(AcmeProperties.class)
public class MyConfiguration {
}
```

To use configuration property scanning, add the `@ConfigurationPropertiesScan` annotation to your application.

- Typically, it is added to the main application class that is annotated with `@SpringBootApplication` but it can be added to any `@Configuration` class. 

```java
@SpringBootApplication
@ConfigurationPropertiesScan({ "com.example.app", "org.acme.another" })
public class MyApplication {
}
```

>  When the `@ConfigurationProperties` bean is registered using configuration property scanning or via `@EnableConfigurationProperties`, the bean has a conventional name: `<prefix>-<fqn>`, where `<prefix>` is the environment key prefix specified in the `@ConfigurationProperties` annotation and `<fqn>` is the fully qualified name of the bean. If the annotation does not provide any prefix, only the fully qualified name of the bean is used.The bean name in the example above is `acme-com.example.AcmeProperties`.



### 3.2.3. Third-party Configuration









# 4. Calling REST Services with RestTemplate

- If you need to call remote REST services from your application, you can use the Spring Framework’s [`RestTemplate`](https://docs.spring.io/spring/docs/5.3.13/javadoc-api/org/springframework/web/client/RestTemplate.html) class
- Since `RestTemplate` instances often need to be customized before being used, Spring Boot does not provide any single auto-configured `RestTemplate` bean
- It does, however, auto-configure a `RestTemplateBuilder`, which can be used to create `RestTemplate` instances when needed

> `RestTemplateBuilder` includes a number of useful methods that can be used to quickly configure a `RestTemplate`. For example, to add BASIC auth support, you can use `builder.basicAuthentication("user", "password").build()`.



```java
package org.springframework.boot.autoconfigure.web.client;

import java.util.stream.Collectors;

import org.springframework.beans.factory.ObjectProvider;
import org.springframework.boot.autoconfigure.AutoConfigureAfter;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.boot.autoconfigure.condition.ConditionalOnClass;
import org.springframework.boot.autoconfigure.condition.ConditionalOnMissingBean;
import org.springframework.boot.autoconfigure.condition.ConditionalOnWebApplication;
import org.springframework.boot.autoconfigure.condition.ConditionalOnWebApplication.Type;
import org.springframework.boot.autoconfigure.condition.NoneNestedConditions;
import org.springframework.boot.autoconfigure.http.HttpMessageConverters;
import org.springframework.boot.autoconfigure.http.HttpMessageConvertersAutoConfiguration;
import org.springframework.boot.autoconfigure.web.client.RestTemplateAutoConfiguration.NotReactiveWebApplicationCondition;
import org.springframework.boot.web.client.RestTemplateBuilder;
import org.springframework.boot.web.client.RestTemplateCustomizer;
import org.springframework.boot.web.client.RestTemplateRequestCustomizer;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Conditional;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Lazy;
import org.springframework.web.client.RestTemplate;

/**
 * {@link EnableAutoConfiguration Auto-configuration} for {@link RestTemplate}.
 *
 * @author Stephane Nicoll
 * @author Phillip Webb
 * @since 1.4.0
 */
@Configuration(proxyBeanMethods = false)
@AutoConfigureAfter(HttpMessageConvertersAutoConfiguration.class)
@ConditionalOnClass(RestTemplate.class)
@Conditional(NotReactiveWebApplicationCondition.class)
public class RestTemplateAutoConfiguration {

	@Bean
	@Lazy
	@ConditionalOnMissingBean
	public RestTemplateBuilderConfigurer restTemplateBuilderConfigurer(
			ObjectProvider<HttpMessageConverters> messageConverters,
			ObjectProvider<RestTemplateCustomizer> restTemplateCustomizers,
			ObjectProvider<RestTemplateRequestCustomizer<?>> restTemplateRequestCustomizers) {
		RestTemplateBuilderConfigurer configurer = new RestTemplateBuilderConfigurer();
		configurer.setHttpMessageConverters(messageConverters.getIfUnique());
		configurer.setRestTemplateCustomizers(restTemplateCustomizers.orderedStream().collect(Collectors.toList()));
		configurer.setRestTemplateRequestCustomizers(
				restTemplateRequestCustomizers.orderedStream().collect(Collectors.toList()));
		return configurer;
	}

	@Bean
	@Lazy
	@ConditionalOnMissingBean
	public RestTemplateBuilder restTemplateBuilder(RestTemplateBuilderConfigurer restTemplateBuilderConfigurer) {
		RestTemplateBuilder builder = new RestTemplateBuilder();
		return restTemplateBuilderConfigurer.configure(builder);
	}

	static class NotReactiveWebApplicationCondition extends NoneNestedConditions {

		NotReactiveWebApplicationCondition() {
			super(ConfigurationPhase.PARSE_CONFIGURATION);
		}

		@ConditionalOnWebApplication(type = Type.REACTIVE)
		private static class ReactiveWebApplication {

		}

	}

}
```



## 4.1. RestTemplate Customization

- To make the scope of any customizations as narrow as possible, inject the auto-configured `RestTemplateBuilder` and then call its methods as required\
- The most extreme (and rarely used) option is to create your own `RestTemplateBuilder` bean without using a configurer



you can configure restTemplate like this:

```java
@Bean
    public RestTemplate restTemplate(RestTemplateBuilder builder){
        return builder
                .setConnectTimeout(Duration.ofMillis(1000L))
                .setReadTimeout(Duration.ofMillis(2000L))
                .build();
    }
```



# 5. Task Execution and Scheduling



- `@EnableAsync`  -> task execution -> `ThreadPoolTaskExecutor`
- `@EnableScheduling` -> task scheduling -> `ThreadPoolTaskScheduler`



```yaml
spring:  
  task:
    execution:
      pool:
        core-size: 10
        max-size: 100
        keep-alive: 60s
        queue-capacity: 100
      thread-name-prefix: covey-thread-pool
    # 定时线程池配置
    scheduling:
      pool:
        size: 10
      thread-name-prefix: covey-scheduling
```

