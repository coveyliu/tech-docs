<!-- TOC -->

- [1. IOC](#1-ioc)
    - [1.1. Introduction to the Spring IoC Container and Beanss](#11-introduction-to-the-spring-ioc-container-and-beanss)
    - [1.2. Container Overvieww](#12-container-overvieww)
        - [1.2.1. Configuration Metadata](#121-configuration-metadata)
        - [1.2.2. Instantiating a Container](#122-instantiating-a-container)
    - [1.3. Bean Overview](#13-bean-overview)
        - [1.3.1. Instantiating Beans](#131-instantiating-beans)
        - [1.3.2. Determining a Bean’s Runtime Type](#132-determining-a-beans-runtime-type)
    - [1.4. Dependencies](#14-dependencies)
        - [1.4.1. Dependency Injection](#141-dependency-injection)
        - [1.4.2. Using depends-on](#142-using-depends-on)
        - [1.4.3. Lazy-initialized Beans](#143-lazy-initialized-beans)
    - [1.5. Method Injection](#15-method-injection)
        - [1.5.1. Lookup Method Injection](#151-lookup-method-injection)
    - [1.6. Customizing the Nature of a Bean](#16-customizing-the-nature-of-a-bean)
        - [1.6.1. Lifecycle Callbacks](#161-lifecycle-callbacks)
        - [1.6.2. Combining Lifecycle Mechanisms](#162-combining-lifecycle-mechanisms)
        - [1.6.3. ApplicationContextAware and BeanNameAware](#163-applicationcontextaware-and-beannameaware)
        - [1.6.4. Other Aware Interfacess](#164-other-aware-interfacess)
    - [1.7. Container Extension Points](#17-container-extension-points)
        - [1.7.1. Customizing Beans by Using a BeanPostProcessor](#171-customizing-beans-by-using-a-beanpostprocessor)
            - [1.7.1.1. Example: The AutowiredAnnotationBeanPostProcessor](#1711-example-the-autowiredannotationbeanpostprocessor)
        - [1.7.2. Customizing Configuration Metadata with a BeanFactoryPostProcessor](#172-customizing-configuration-metadata-with-a-beanfactorypostprocessor)
            - [1.7.2.1. Example: The Class Name Substitution PropertySourcesPlaceholderConfigurer](#1721-example-the-class-name-substitution-propertysourcesplaceholderconfigurer)
        - [1.7.3. Customizing Instantiation Logic with a FactoryBean](#173-customizing-instantiation-logic-with-a-factorybean)
    - [1.8. Java-based Container Configuration](#18-java-based-container-configuration)
        - [1.8.1. Basic Concepts: @Bean and @Configuration](#181-basic-concepts-bean-and-configuration)
        - [1.8.2. Instantiating the Spring Container by Using AnnotationConfigApplicationContext](#182-instantiating-the-spring-container-by-using-annotationconfigapplicationcontext)
            - [1.8.2.1. Building the Container Programmatically by Using registerClass<?>…](#1821-building-the-container-programmatically-by-using-registerclass)
        - [1.8.3. Using the @Configuration annotation](#183-using-the-configuration-annotation)
    - [1.9. Environment Abstractionn](#19-environment-abstractionn)
        - [1.9.1. Environment Abstractionn](#191-environment-abstractionn)
        - [1.9.2. Using @PropertySource](#192-using-propertysource)
    - [1.10. Additional Capabilities of the ApplicationContext](#110-additional-capabilities-of-the-applicationcontext)
- [2. AOP](#2-aop)
    - [2.1. AOP Concepts](#21-aop-concepts)
    - [2.2. Spring AOP Capabilities and Goals](#22-spring-aop-capabilities-and-goals)
    - [2.3. AOP Proxies](#23-aop-proxies)
    - [2.4. @AspectJ support](#24-aspectj-support)
        - [2.4.1. Enabling @AspectJ Support](#241-enabling-aspectj-support)
    - [2.5. Proxying Mechanisms](#25-proxying-mechanisms)
        - [2.5.1. Understanding AOP Proxies](#251-understanding-aop-proxies)
    - [2.6. Programmatic Creation of @AspectJ Proxies](#26-programmatic-creation-of-aspectj-proxies)
    - [2.7. Spring AOP APIs](#27-spring-aop-apis)
        - [2.7.1. Pointcut API in Spring](#271-pointcut-api-in-spring)
            - [2.7.1.1. Concepts](#2711-concepts)
            - [2.7.1.2. Operations on Pointcuts](#2712-operations-on-pointcuts)
        - [2.7.2. Using the ProxyFactoryBean to Create AOP Proxies](#272-using-the-proxyfactorybean-to-create-aop-proxies)
        - [2.7.3. Creating AOP Proxies Programmatically with the ProxyFactory](#273-creating-aop-proxies-programmatically-with-the-proxyfactory)
        - [2.7.4. Using the "auto-proxy" facility](#274-using-the-auto-proxy-facility)

<!-- /TOC -->
# 1. IOC

## 1.1. Introduction to the Spring IoC Container and Beanss

- The `org.springframework.beans` and `org.springframework.context` packages are the basis for Spring Framework’s IoC container
- The [`BeanFactory`](https://docs.spring.io/spring-framework/docs/5.2.22.RELEASE/javadoc-api/org/springframework/beans/factory/BeanFactory.html) interface provides an advanced configuration mechanism capable of managing any type of object.
- [`ApplicationContext`](https://docs.spring.io/spring-framework/docs/5.2.22.RELEASE/javadoc-api/org/springframework/context/ApplicationContext.html) is a sub-interface of `BeanFactory`. It adds:
  - Easier integration with Spring’s AOP features
  - Message resource handling (for use in internationalization)
  - Event publication
  - Application-layer specific contexts such as the `WebApplicationContext` for use in web applications

## 1.2. Container Overvieww



### 1.2.1. Configuration Metadata

The Spring IoC container consumes a form of configuration metadata. This configuration metadata represents how you, as an application developer, tell the Spring container to instantiate, configure, and assemble the objects in your application.



Three forms of metadata with the Spring container

- XML-based configuration metadata
- [Annotation-based configuration](https://docs.spring.io/spring-framework/docs/5.2.22.RELEASE/spring-framework-reference/core.html#beans-annotation-config): Spring 2.5 introduced support for annotation-based configuration metadata.
- [Java-based configuration](https://docs.spring.io/spring-framework/docs/5.2.22.RELEASE/spring-framework-reference/core.html#beans-java): Starting with Spring 3.0, many features provided by the Spring JavaConfig project became part of the core Spring Framework. Thus, you can define beans external to your application classes by using Java rather than XML files. To use these new features, see the [`@Configuration`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Configuration.html), [`@Bean`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Bean.html), [`@Import`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Import.html), and [`@DependsOn`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/DependsOn.html) annotations.



### 1.2.2. Instantiating a Container

```java
ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");
```





## 1.3. Bean Overview

### 1.3.1. Instantiating Beans

- Instantiation with a Constructor
- Instantiation with a Static Factory Method
- Instantiation by Using an Instance Factory Method.(`@Bean`)



**Instantiation with a Static Factory Method**

```xml
<bean id="clientService"
    class="examples.ClientService"
    factory-method="createInstance"/>
```

```java
public class ClientService {
    private static ClientService clientService = new ClientService();
    private ClientService() {}

    public static ClientService createInstance() {
        return clientService;
    }
}
```





**Instantiation by Using an Instance Factory Method**

```xml
<!-- the factory bean, which contains a method called createInstance() -->
<bean id="serviceLocator" class="examples.DefaultServiceLocator">
    <!-- inject any dependencies required by this locator bean -->
</bean>

<!-- the bean to be created via the factory bean -->
<bean id="clientService"
    factory-bean="serviceLocator"
    factory-method="createClientServiceInstance"/>
```



### 1.3.2. Determining a Bean’s Runtime Type



The runtime type of a specific bean is non-trivial to determine. The recommended way to find out about the actual runtime type of a particular bean is a `BeanFactory.getType` call for the specified bean name





## 1.4. Dependencies



### 1.4.1. Dependency Injection

- DI exists in two major variants: [Constructor-based dependency injection](https://docs.spring.io/spring-framework/docs/5.2.22.RELEASE/spring-framework-reference/core.html#beans-constructor-injection) and [Setter-based dependency injection](https://docs.spring.io/spring-framework/docs/5.2.22.RELEASE/spring-framework-reference/core.html#beans-setter-injection).
- If you use predominantly constructor injection, it is possible to create an unresolvable circular dependency scenario. One possible solution is to edit the source code of some classes to be configured by setters rather than constructors. Alternatively, avoid constructor injection and use setter injection only
- ==Dependency Resolution Process==
  - For each bean, its dependencies are expressed in the form of properties, constructor arguments, or arguments to the static-factory method
  - The Spring container validates the configuration of each bean as the container is created. However, the bean properties themselves are not set until the bean is actually created
  - Beans that are singleton-scoped and set to be pre-instantiated (the default) are created when the container is created. Scopes are defined in [Bean Scopes](https://docs.spring.io/spring-framework/docs/5.2.22.RELEASE/spring-framework-reference/core.html#beans-factory-scopes). Otherwise, the bean is created only when it is requested
  - Creation of a bean potentially causes a graph of beans to be created, as the bean’s dependencies and its dependencies' dependencies (and so on) are created and assigned.



```java
public class SimpleMovieLister {

    // the SimpleMovieLister has a dependency on a MovieFinder
    private final MovieFinder movieFinder;

    // a constructor so that the Spring container can inject a MovieFinder
    public SimpleMovieLister(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

    // business logic that actually uses the injected MovieFinder is omitted...
}
```





```java
public class SimpleMovieLister {

    // the SimpleMovieLister has a dependency on the MovieFinder
    private MovieFinder movieFinder;

    // a setter method so that the Spring container can inject a MovieFinder
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

    // business logic that actually uses the injected MovieFinder is omitted...
}
```





### 1.4.2. Using `depends-on`

If a bean is a dependency of another bean, that usually means that one bean is set as a property of another. Typically you accomplish this with the` <ref/>` element  in XML-based configuration metadata. However, sometimes dependencies between beans are less direct

The `depends-on` attribute can explicitly force one or more beans to be initialized before the bean using this element is initialized



```xml
<bean id="beanOne" class="ExampleBean" depends-on="manager,accountDao">
    <property name="manager" ref="manager" />
</bean>

<bean id="manager" class="ManagerBean" />
<bean id="accountDao" class="x.y.jdbc.JdbcAccountDao" />
```



### 1.4.3. Lazy-initialized Beans

- Generally, this pre-instantiation is desirable, because errors in the configuration or surrounding environment are discovered immediately
- A lazy-initialized bean tells the IoC container to create a bean instance when it is first requested, rather than at startup.



Example :

```java
<bean id="lazy" class="com.something.ExpensiveToCreateBean" lazy-init="true"/>
<bean name="not.lazy" class="com.something.AnotherBean"/>
```





## 1.5. Method Injection

Suppose singleton bean A needs to use non-singleton (prototype) bean B, perhaps on each method invocation on A. The container creates the singleton bean A only once, and thus only gets one opportunity to set the properties. The container cannot provide bean A with a new instance of bean B every time one is needed.



Solution A:

```java
public class CommandManager implements ApplicationContextAware {

    private ApplicationContext applicationContext;

    public Object process(Map commandState) {
        // grab a new instance of the appropriate Command
        Command command = createCommand();
        // set the state on the (hopefully brand new) Command instance
        command.setState(commandState);
        return command.execute();
    }

    protected Command createCommand() {
        // notice the Spring API dependency!
        return this.applicationContext.getBean("command", Command.class);
    }

    public void setApplicationContext(
            ApplicationContext applicationContext) throws BeansException {
        this.applicationContext = applicationContext;
    }
}
```



### 1.5.1. Lookup Method Injection



```java
public abstract class CommandManager {

    public Object process(Object commandState) {
        // grab a new instance of the appropriate Command interface
        Command command = createCommand();
        // set the state on the (hopefully brand new) Command instance
        command.setState(commandState);
        return command.execute();
    }

    // okay... but where is the implementation of this method?
    protected abstract Command createCommand();
}
```



xml-based:

```xml
<!-- a stateful bean deployed as a prototype (non-singleton) -->
<bean id="myCommand" class="fiona.apple.AsyncCommand" scope="prototype">
    <!-- inject dependencies here as required -->
</bean>

<!-- commandProcessor uses statefulCommandHelper -->
<bean id="commandManager" class="fiona.apple.CommandManager">
    <lookup-method name="createCommand" bean="myCommand"/>
</bean>
```



Alternatively, within the annotation-based component model, you can declare a lookup method through the `@Lookup` annotation, as the following example shows:

```java
public abstract class CommandManager {

    public Object process(Object commandState) {
        Command command = createCommand();
        command.setState(commandState);
        return command.execute();
    }

    @Lookup
    protected abstract Command createCommand();
}
```



## 1.6. Customizing the Nature of a Bean

The Spring Framework provides a number of interfaces you can use to customize the nature of a bean. This section groups them as follows:

- [Lifecycle Callbacks](https://docs.spring.io/spring-framework/docs/5.2.22.RELEASE/spring-framework-reference/core.html#beans-factory-lifecycle)(`org.springframework.beans.factory.InitializingBean`, `org.springframework.beans.factory.DisposableBean`)
- `Aware` Interfaces
  - [`ApplicationContextAware` and `BeanNameAware`](https://docs.spring.io/spring-framework/docs/5.2.22.RELEASE/spring-framework-reference/core.html#beans-factory-aware)
  - [Other `Aware` Interfaces](https://docs.spring.io/spring-framework/docs/5.2.22.RELEASE/spring-framework-reference/core.html#aware-list)



### 1.6.1. Lifecycle Callbacks



**Initialization Callbacks**

1. implement `InitializingBean` interface
2. Use the [`@PostConstruct`](https://docs.spring.io/spring-framework/docs/5.2.22.RELEASE/spring-framework-reference/core.html#beans-postconstruct-and-predestroy-annotations) annotation
3. Use the `initMethod` attribute of `@Bean`



**Destruction Callbacks**

1. Implementing the `org.springframework.beans.factory.DisposableBean` interface
2. Using the [`@PreDestroy`](https://docs.spring.io/spring-framework/docs/5.2.22.RELEASE/spring-framework-reference/core.html#beans-postconstruct-and-predestroy-annotations) annotation 
3. Using the `destroyMethod` attribute of `@Bean`



**Default Initialization and Destroy Methods**

- You can configure the Spring container to “look” for named initialization and destroy callback method names on every bean
- Ideally, the names of such lifecycle callback methods are standardized across a project so that all developers use the same method names and ensure consistency.

```java
public class DefaultBlogService implements BlogService {

    private BlogDao blogDao;

    public void setBlogDao(BlogDao blogDao) {
        this.blogDao = blogDao;
    }

    // this is (unsurprisingly) the initialization callback method
    public void init() {
        if (this.blogDao == null) {
            throw new IllegalStateException("The [blogDao] property must be set.");
        }
    }
}
```

You could then use that class in a bean resembling the following:

```xml
<beans default-init-method="init">

    <bean id="blogService" class="com.something.DefaultBlogService">
        <property name="blogDao" ref="blogDao" />
    </bean>

</beans>
```

The presence of the `default-init-method` attribute on the top-level `<beans/>` element attribute causes the Spring IoC container to recognize a method called `init` on the bean class as the initialization method callback. When a bean is created and assembled, if the bean class has such a method, it is invoked at the appropriate time.



### 1.6.2. Combining Lifecycle Mechanisms

As of Spring 2.5, you have three options for controlling bean lifecycle behavior:

- The [`InitializingBean`](https://docs.spring.io/spring-framework/docs/5.2.22.RELEASE/spring-framework-reference/core.html#beans-factory-lifecycle-initializingbean) and [`DisposableBean`](https://docs.spring.io/spring-framework/docs/5.2.22.RELEASE/spring-framework-reference/core.html#beans-factory-lifecycle-disposablebean) callback interfaces
- Custom `init()` and `destroy()` methods
- The [`@PostConstruct` and `@PreDestroy` annotations](https://docs.spring.io/spring-framework/docs/5.2.22.RELEASE/spring-framework-reference/core.html#beans-postconstruct-and-predestroy-annotations). You can combine these mechanisms to control a given bean.



Multiple lifecycle mechanisms configured for the same bean, with different initialization methods, are called as follows:

1. Methods annotated with `@PostConstruct`
2. `afterPropertiesSet()` as defined by the `InitializingBean` callback interface
3. A custom configured `init()` method

Destroy methods are called in the same order:

1. Methods annotated with `@PreDestroy`
2. `destroy()` as defined by the `DisposableBean` callback interface
3. A custom configured `destroy()` method





### 1.6.3. `ApplicationContextAware` and `BeanNameAware`



- implement the `org.springframework.context.ApplicationContextAware` interface

- Autowiring is another alternative to obtain a reference to the `ApplicationContext`. The *traditional* `constructor` and `byType` autowiring modes (as described in [Autowiring Collaborators](https://docs.spring.io/spring-framework/docs/5.2.22.RELEASE/spring-framework-reference/core.html#beans-factory-autowire)) can provide a dependency of type `ApplicationContext` for a constructor argument or a setter method parameter, respectively
- If you do, the `ApplicationContext` is autowired into a field, constructor argument, or method parameter that expects the `ApplicationContext` type if the field, constructor, or method in question carries the `@Autowired` annotation



### 1.6.4. Other `Aware` Interfacess

The following table summarizes the most important `Aware` interfaces:

| Name                             | Injected Dependency                                          | Explained in…                                                |
| :------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| `ApplicationContextAware`        | Declaring `ApplicationContext`.                              | [`ApplicationContextAware` and `BeanNameAware`](https://docs.spring.io/spring-framework/docs/5.2.22.RELEASE/spring-framework-reference/core.html#beans-factory-aware) |
| `ApplicationEventPublisherAware` | Event publisher of the enclosing `ApplicationContext`.       | [Additional Capabilities of the `ApplicationContext`](https://docs.spring.io/spring-framework/docs/5.2.22.RELEASE/spring-framework-reference/core.html#context-introduction) |
| `BeanClassLoaderAware`           | Class loader used to load the bean classes.                  | [Instantiating Beans](https://docs.spring.io/spring-framework/docs/5.2.22.RELEASE/spring-framework-reference/core.html#beans-factory-class) |
| `BeanFactoryAware`               | Declaring `BeanFactory`.                                     | [The `BeanFactory`](https://docs.spring.io/spring-framework/docs/5.2.22.RELEASE/spring-framework-reference/core.html#beans-beanfactory) |
| `BeanNameAware`                  | Name of the declaring bean.                                  | [`ApplicationContextAware` and `BeanNameAware`](https://docs.spring.io/spring-framework/docs/5.2.22.RELEASE/spring-framework-reference/core.html#beans-factory-aware) |
| `BootstrapContextAware`          | Resource adapter `BootstrapContext` the container runs in. Typically available only in JCA-aware `ApplicationContext` instances. | [JCA CCI](https://docs.spring.io/spring-framework/docs/5.2.22.RELEASE/spring-framework-reference/integration.html#cci) |
| `LoadTimeWeaverAware`            | Defined weaver for processing class definition at load time. | [Load-time Weaving with AspectJ in the Spring Framework](https://docs.spring.io/spring-framework/docs/5.2.22.RELEASE/spring-framework-reference/core.html#aop-aj-ltw) |
| `MessageSourceAware`             | Configured strategy for resolving messages (with support for parametrization and internationalization). | [Additional Capabilities of the `ApplicationContext`](https://docs.spring.io/spring-framework/docs/5.2.22.RELEASE/spring-framework-reference/core.html#context-introduction) |
| `NotificationPublisherAware`     | Spring JMX notification publisher.                           | [Notifications](https://docs.spring.io/spring-framework/docs/5.2.22.RELEASE/spring-framework-reference/integration.html#jmx-notifications) |
| `ResourceLoaderAware`            | Configured loader for low-level access to resources.         | [Resources](https://docs.spring.io/spring-framework/docs/5.2.22.RELEASE/spring-framework-reference/core.html#resources) |
| `ServletConfigAware`             | Current `ServletConfig` the container runs in. Valid only in a web-aware Spring `ApplicationContext`. | [Spring MVC](https://docs.spring.io/spring-framework/docs/5.2.22.RELEASE/spring-framework-reference/web.html#mvc) |
| `ServletContextAware`            | Current `ServletContext` the container runs in. Valid only in a web-aware Spring `ApplicationContext`. | [Spring MV](https://docs.spring.io/spring-framework/docs/5.2.22.RELEASE/spring-framework-reference/web.html#mvc) |



## 1.7. Container Extension Points

### 1.7.1. Customizing Beans by Using a `BeanPostProcessor`

- If you want to implement some custom logic after the Spring container finishes instantiating, configuring, and initializing a bean, you can plug in one or more custom `BeanPostProcessor` implementations.
- The `org.springframework.beans.factory.config.BeanPostProcessor` interface consists of exactly two callback methods:
  - **before container initialization methods** (such as `InitializingBean.afterPropertiesSet()` or any declared `init` method) are called
  - **after any bean initialization callbacks**. The post-processor can take any action with the bean instance
- A bean post-processor typically checks for callback interfaces, or it may wrap a bean with a proxy. Some Spring AOP infrastructure classes are implemented as bean post-processors in order to provide proxy-wrapping logic.
- You can configure multiple `BeanPostProcessor` instances, and you can control the order in which these `BeanPostProcessor` instances run by setting the `order` property.
- how to write, register, and use `BeanPostProcessor` instances in an `ApplicationContext`
  - An `ApplicationContext` automatically detects any beans that are defined in the configuration metadata that implement the `BeanPostProcessor` interface
  - when declaring a `BeanPostProcessor` by using an `@Bean` factory method on a configuration class, the return type of the factory method should be the implementation class itself or at least the



```java
public interface BeanPostProcessor {

	/**
	 * Apply this {@code BeanPostProcessor} to the given new bean instance <i>before</i> any bean
	 * initialization callbacks (like InitializingBean's {@code afterPropertiesSet}
	 * or a custom init-method). The bean will already be populated with property values.
	 * The returned bean instance may be a wrapper around the original.
	 * <p>The default implementation returns the given {@code bean} as-is.
	 * @param bean the new bean instance
	 * @param beanName the name of the bean
	 * @return the bean instance to use, either the original or a wrapped one;
	 * if {@code null}, no subsequent BeanPostProcessors will be invoked
	 * @throws org.springframework.beans.BeansException in case of errors
	 * @see org.springframework.beans.factory.InitializingBean#afterPropertiesSet
	 */
	@Nullable
	default Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
		return bean;
	}

	/**
	 * Apply this {@code BeanPostProcessor} to the given new bean instance <i>after</i> any bean
	 * initialization callbacks (like InitializingBean's {@code afterPropertiesSet}
	 * or a custom init-method). The bean will already be populated with property values.
	 * The returned bean instance may be a wrapper around the original.
	 * <p>In case of a FactoryBean, this callback will be invoked for both the FactoryBean
	 * instance and the objects created by the FactoryBean (as of Spring 2.0). The
	 * post-processor can decide whether to apply to either the FactoryBean or created
	 * objects or both through corresponding {@code bean instanceof FactoryBean} checks.
	 * <p>This callback will also be invoked after a short-circuiting triggered by a
	 * {@link InstantiationAwareBeanPostProcessor#postProcessBeforeInstantiation} method,
	 * in contrast to all other {@code BeanPostProcessor} callbacks.
	 * <p>The default implementation returns the given {@code bean} as-is.
	 * @param bean the new bean instance
	 * @param beanName the name of the bean
	 * @return the bean instance to use, either the original or a wrapped one;
	 * if {@code null}, no subsequent BeanPostProcessors will be invoked
	 * @throws org.springframework.beans.BeansException in case of errors
	 * @see org.springframework.beans.factory.InitializingBean#afterPropertiesSet
	 * @see org.springframework.beans.factory.FactoryBean
	 */
	@Nullable
	default Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
		return bean;
	}

}
```





#### 1.7.1.1. Example: The `AutowiredAnnotationBeanPostProcessor``

- Using callback interfaces or annotations in conjunction with a custom `BeanPostProcessor` implementation is a common means of extending the Spring IoC container. 
- An example is Spring’s `AutowiredAnnotationBeanPostProcessor` — a `BeanPostProcessor` implementation tautowires annotated fields, setter methods, and arbitrary config methods.



### 1.7.2. Customizing Configuration Metadata with a `BeanFactoryPostProcessor`



`org.springframework.beans.factory.config.BeanFactoryPostProcessor`. The semantics of this interface are similar to those of the `BeanPostProcessor`, with one major difference: `BeanFactoryPostProcessor` operates on the bean configuration metadata. That is, the Spring IoC container lets a `BeanFactoryPostProcessor` read the configuration metadata and potentially change it *before* the container instantiates any beans other than `BeanFactoryPostProcessor` instances.

(比如，将 bean configuration metadata 中的占位符，解析出来)

```java
@FunctionalInterface
public interface BeanFactoryPostProcessor {

    /**
     * Modify the application context's internal bean factory after its standard
     * initialization. All bean definitions will have been loaded, but no beans
     * will have been instantiated yet. This allows for overriding or adding
     * properties even to eager-initializing beans.
     * @param beanFactory the bean factory used by the application context
     * @throws org.springframework.beans.BeansException in case of errors
     */
    void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException;

}
```



#### 1.7.2.1. Example: The Class Name Substitution `PropertySourcesPlaceholderConfigurer`

```xml
<bean class="org.springframework.context.support.PropertySourcesPlaceholderConfigurer">
    <property name="locations" value="classpath:com/something/jdbc.properties"/>
</bean>

<bean id="dataSource" destroy-method="close"
        class="org.apache.commons.dbcp.BasicDataSource">
    <property name="driverClassName" value="${jdbc.driverClassName}"/>
    <property name="url" value="${jdbc.url}"/>
    <property name="username" value="${jdbc.username}"/>
    <property name="password" value="${jdbc.password}"/>
</bean>
```





### 1.7.3. Customizing Instantiation Logic with a `FactoryBean`

If you have complex initialization code that is better expressed in Java as opposed to a (potentially) verbose amount of XML, you can create your own `FactoryBean`, write the complex initialization inside that class, and then plug your custom `FactoryBean` into the container.



## 1.8. Java-based Container Configuration

### 1.8.1. Basic Concepts: `@Bean` and `@Configuration`

-  For those familiar with Spring’s `<beans/>` XML configuration, the `@Bean` annotation plays the same role as the `<bean/>` element
- You can use `@Bean`-annotated methods with any Spring `@Component`. However, they are most often used with `@Configuration` beans.
- Annotating a class with `@Configuration` indicates that its primary purpose is as a source of bean definitions



### 1.8.2. Instantiating the Spring Container by Using `AnnotationConfigApplicationContext``

The following sections document Spring’s `AnnotationConfigApplicationContext`, introduced in Spring 3.0. This versatile `ApplicationContext` implementation is capable of accepting

-  `@Configuration` classes as input 
-  plain `@Component` classes and classes annotated with JSR-330 metadata.



```java
@Configuration
public class AppConfig {

    @Bean
    public MyService myService() {
        return new MyServiceImpl();
    }
}
```



```java
public static void main(String[] args) {
    ApplicationContext ctx = new AnnotationConfigApplicationContext(AppConfig.class);
    MyService myService = ctx.getBean(MyService.class);
    myService.doStuff();
}
```





#### 1.8.2.1. Building the Container Programmatically by Using `register(Class<?>…)`

You can instantiate an `AnnotationConfigApplicationContext` by using a no-arg constructor and then configure it by using the `register()` method. This approach is particularly useful when programmatically building an `AnnotationConfigApplicationContext`. The following example shows how to do so:



```java
@Configuration
@ComponentScan(basePackages = "com.acme") 
public class AppConfig  {
    // ...
}
```



```java
public static void main(String[] args) {
    AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext();
    ctx.register(AppConfig.class, OtherConfig.class);
    ctx.register(AdditionalConfig.class);
    ctx.refresh();
    MyService myService = ctx.getBean(MyService.class);
    myService.doStuff();
}
```

In the preceding example, the `com.acme` package is scanned to look for any `@Component`-annotated classes, and those classes are registered as Spring bean definitions within the container. `AnnotationConfigApplicationContext` exposes the `scan(String…)` method to allow for the same component-scanning functionality, as the following example shows:

```java
public static void main(String[] args) {
    AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext();
    ctx.scan("com.acme");
    ctx.refresh();
    MyService myService = ctx.getBean(MyService.class);
}
```



### 1.8.3. Using the `@Configuration` annotation



 **Injecting Inter-bean Dependencies**

```java
@Configuration
public class AppConfig {

    @Bean
    public BeanOne beanOne() {
        return new BeanOne(beanTwo());
    }

    @Bean
    public BeanTwo beanTwo() {
        return new BeanTwo();
    }
}
```

 This method of declaring inter-bean dependencies works only when the `@Bean` method is declared within a `@Configuration` class. You cannot declare inter-bean dependencies by using plain `@Component` classes.



**Further Information About How Java-based Configuration Works Internally**

```java
@Configuration
public class AppConfig {

    @Bean
    public ClientService clientService1() {
        ClientServiceImpl clientService = new ClientServiceImpl();
        clientService.setClientDao(clientDao());
        return clientService;
    }

    @Bean
    public ClientService clientService2() {
        ClientServiceImpl clientService = new ClientServiceImpl();
        clientService.setClientDao(clientDao());
        return clientService;
    }

    @Bean
    public ClientDao clientDao() {
        return new ClientDaoImpl();
    }
}
```

All `@Configuration` classes are subclassed at startup-time with `CGLIB`. In the subclass, the child method checks the container first for any cached (scoped) beans before it calls the parent method and creates a new instance

If you prefer to avoid any CGLIB-imposed limitations, consider declaring your `@Bean` methods on non-`@Configuration` classes (for example, on plain `@Component` classes instead). Cross-method calls between `@Bean` methods are not then intercepted, so you have to exclusively rely on dependency injection at the constructor or method level there





## 1.9. Environment Abstractionn

### 1.9.1. Environment Abstractionn

```java
ApplicationContext ctx = new GenericApplicationContext();
Environment env = ctx.getEnvironment();
boolean containsMyProperty = env.containsProperty("my-property");
System.out.println("Does my environment contain the 'my-property' property? " + containsMyProperty);
```



- `Environment` object performs a search over a set of [`PropertySource`](https://docs.spring.io/spring-framework/docs/5.2.22.RELEASE/javadoc-api/org/springframework/core/env/PropertySource.html) objects.
- A `PropertySource` is a simple abstraction over any source of key-value pairs
- Spring’s [`StandardEnvironment`](https://docs.spring.io/spring-framework/docs/5.2.22.RELEASE/javadoc-api/org/springframework/core/env/StandardEnvironment.html) is configured with two PropertySource objects — one representing the set of JVM system properties (`System.getProperties()`) and one representing the set of system environment variables (`System.getenv()`).



### 1.9.2. Using `@PropertySource`

The [`@PropertySource`](https://docs.spring.io/spring-framework/docs/5.2.22.RELEASE/javadoc-api/org/springframework/context/annotation/PropertySource.html) annotation provides a convenient and declarative mechanism for adding a `PropertySource` to Spring’s `Environment`



```java
@Configuration
@PropertySource("classpath:/com/myco/app.properties")
public class AppConfig {

    @Autowired
    Environment env;

    @Bean
    public TestBean testBean() {
        TestBean testBean = new TestBean();
        testBean.setName(env.getProperty("testbean.name"));
        return testBean;
    }
}
```



## 1.10. Additional Capabilities of the `ApplicationContext`

- Access to messages in i18n-style, through the `MessageSource` interface.
- Access to resources, such as URLs and files, through the `ResourceLoader` interface.
- Event publication, namely to beans that implement the `ApplicationListener` interface, through the use of the `ApplicationEventPublisher` interface.





# 2. AOP

## 2.1. AOP Concepts

- **Join point:** A point during the execution of a program, such as the execution of a method or the handling of an exception. In Spring AOP, a join point always represents a method execution
- **Advice**: Action taken by an aspect at a particular join point. Different types of advice include “around”, “before” and “after” advice. (Advice types are discussed later.) Many AOP frameworks, including Spring, model an advice as an interceptor and maintain a chain of interceptors around the join point.
- **Pointcut**: A predicate that matches join points. Advice is associated with a pointcut expression and runs at any join point matched by the pointcut (for example, the execution of a method with a certain name). The concept of join points as matched by pointcut expressions is central to AOP, and Spring uses the AspectJ pointcut expression language by default。（可以理解为条件）
- Target object: An object being advised by one or more aspects. Also referred to as the “advised object”. Since Spring AOP is implemented by using runtime proxies, this object is always a proxied object.
- AOP proxy: An object created by the AOP framework in order to implement the aspect contracts (advise method executions and so on). In the Spring Framework, an AOP proxy is a JDK dynamic proxy or a CGLIB proxy.
- Weaving: linking aspects with other application types or objects to create an advised object. This can be done at compile time (using the AspectJ compiler, for example), load time, or at runtime. Spring AOP, like other pure Java AOP frameworks, performs weaving at runtime.





## 2.2. Spring AOP Capabilities and Goals



- Spring AOP currently supports only method execution join points (advising the execution of methods on Spring beans).
- Spring AOP's  aim is to provide a close integration between AOP implementation and Spring IoC
-  proxy-based frameworks AOP framework



## 2.3. AOP Proxies

- Spring AOP defaults to using standard JDK dynamic proxies for AOP proxies
- Spring AOP can also use CGLIB proxies (By default, CGLIB is used if a business object does not implement an interface)
- it is important to grasp the fact that Spring AOP is proxy-based



## 2.4. @AspectJ support



### 2.4.1. Enabling @AspectJ Support

To use @AspectJ aspects in a Spring configuration, you need to enable Spring support for

-  configuring Spring AOP based on @AspectJ aspects
-  and auto-proxying beans



To enable @AspectJ support with Java `@Configuration`, add the `@EnableAspectJAutoProxy` annotation, as the following example shows:

```java
@Configuration
@EnableAspectJAutoProxy
public class AppConfig {

}
```





## 2.5. Proxying Mechanisms

Spring AOP uses either JDK dynamic proxies or CGLIB to create the proxy for a given target object. JDK dynamic proxies are built into the JDK, whereas CGLIB is a common open-source class definition library (repackaged into `spring-core`).

If the target object to be proxied implements at least one interface, a JDK dynamic proxy is used. All of the interfaces implemented by the target type are proxied. If the target object does not implement any interfaces, a CGLIB proxy is created.

If you want to force the use of CGLIB proxying (for example, to proxy every method defined for the target object, not only those implemented by its interfaces), you can do so. However, you should consider the following issues:

- With CGLIB, `final` methods cannot be advised, as they cannot be overridden in runtime-generated subclasses.
- As of Spring 4.0, the constructor of your proxied object is NOT called twice anymore, since the CGLIB proxy instance is created through Objenesis. Only if your JVM does not allow for constructor bypassing, you might see double invocations and corresponding debug log entries from Spring’s AOP support.



To force CGLIB proxying when you use the @AspectJ auto-proxy support, set the `proxy-target-class` attribute  to `true`

```xml
<aop:config proxy-target-class="true">
    <!-- other beans defined here... -->
</aop:config>
```



```java
/**
* Enables support for handling components marked with AspectJ's {@code @Aspect} annotation,
 * similar to functionality found in Spring's {@code <aop:aspectj-autoproxy>} XML element.
 * To be used on @{@link Configuration} classes as follows:
*/
public @interface EnableAspectJAutoProxy {

	/**
	 * Indicate whether subclass-based (CGLIB) proxies are to be created as opposed
	 * to standard Java interface-based proxies. The default is {@code false}.
	 */
	boolean proxyTargetClass() default false;

	/**
	 * Indicate that the proxy should be exposed by the AOP framework as a {@code ThreadLocal}
	 * for retrieval via the {@link org.springframework.aop.framework.AopContext} class.
	 * Off by default, i.e. no guarantees that {@code AopContext} access will work.
	 * @since 4.3.1
	 */
	boolean exposeProxy() default false;

}

```



### 2.5.1. Understanding AOP Proxies

- self-invocation is not going to result in the advice associated with a method invocation getting a chance to run
- AspectJ does not have this self-invocation issue because it is not a proxy-based AOP framework.





## 2.6. Programmatic Creation of @AspectJ Proxies

Here, we want to focus on the ability to automatically create proxies by using @AspectJ aspects.

You can use the `org.springframework.aop.aspectj.annotation.AspectJProxyFactory` class to create a proxy for a target object that is advised by one or more @AspectJ aspects. The basic usage for this class is very simple, as the following example shows:

```java
// create a factory that can generate a proxy for the given target object
AspectJProxyFactory factory = new AspectJProxyFactory(targetObject);

// add an aspect, the class must be an @AspectJ aspect
// you can call this as many times as you need with different aspects
factory.addAspect(SecurityManager.class);

// you can also add existing aspect instances, the type of the object supplied must be an @AspectJ aspect
factory.addAspect(usageTracker);

// now get the proxy object...
MyInterfaceType proxy = factory.getProxy();
```





## 2.7. Spring AOP APIs

### 2.7.1. Pointcut API in Spring

#### 2.7.1.1. Concepts

```java
public interface Pointcut {

    ClassFilter getClassFilter();

    MethodMatcher getMethodMatcher();

}
```



#### 2.7.1.2. Operations on Pointcuts



- Spring supports operations (notably, union and intersection) on pointcuts.



### 2.7.2. Using the `ProxyFactoryBean` to Create AOP Proxies

The basic way to create an AOP proxy in Spring is to use the `org.springframework.aop.framework.ProxyFactoryBean`. This gives complete control over the pointcuts, any advice that applies, and their ordering. However, there are simpler options that are preferable if you do not need such control.





### 2.7.3. Creating AOP Proxies Programmatically with the `ProxyFactory`

It is easy to create AOP proxies programmatically with Spring. This lets you use Spring AOP without dependency on Spring IoC.

The interfaces implemented by the target object are automatically proxied. The following listing shows creation of a proxy for a target object, with one interceptor and one advisor:

```java
ProxyFactory factory = new ProxyFactory(myBusinessInterfaceImpl);
factory.addAdvice(myMethodInterceptor);
factory.addAdvisor(myAdvisor);
MyBusinessInterface tb = (MyBusinessInterface) factory.getProxy();
```



### 2.7.4. Using the "auto-proxy" facility

So far, we have considered explicit creation of AOP proxies by using a `ProxyFactoryBean` or similar factory bean.

Spring also lets us use “auto-proxy” bean definitions, which can automatically proxy selected bean definitions. This is built on Spring’s “bean post processor” infrastructure, which enables modification of any bean definition as the container loads.

你需要配置（在 beandefinitions）自动代理基础设施（谁负责创建代理）（By using an auto-proxy creator that refers to specific beans in the current context）

<img src="https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20221119122440.png" alt="image-20221119122440763" style="zoom:50%;" />