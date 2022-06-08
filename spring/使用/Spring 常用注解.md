
<!-- TOC -->

- [1. @Configuration](#1-configuration)
    - [1.1. 使用限制](#11-%E4%BD%BF%E7%94%A8%E9%99%90%E5%88%B6)
    - [1.2. 源码分析](#12-%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90)
- [2. @Import](#2-import)
- [3. @Conditional](#3-conditional)
    - [3.1. 检查 - Condition](#31-%E6%A3%80%E6%9F%A5---condition)
    - [3.2. @ConditionalOnClass](#32-conditionalonclass)
    - [3.3. @ConditionalOnProperty](#33-conditionalonproperty)
- [4. @AutoConfigureBefore](#4-autoconfigurebefore)

<!-- /TOC -->




# 1. @Configuration

- 特点：

  1. 标注的类，会被扫描（component scanning）。（本身就是从@Component 衍生的）

  2. 配置要注入 IOC 容器的 bean。两种形式：

     - @Bean 标注方法
     - @ComponentScan，额外配置要扫描的包

  3. 也可以依赖注入

  4. 启用spring 支持的 Enable 模块

     - 异步方法执行，@EnableAsync
     - 定时任务，@EnableScheduling
     - 注解事务管理，@EnableTransactionManagement
     - Aspectj代理，@EnableAspectJAutoProxy
     - 启用springMVC，@EnableWebMvc （==这个不是很懂==)

     



**@Bean 一起使用**

```java
@Configuration
public class AppConfig {

    @Bean
    public MyBean myBean() {
        // instantiate, configure and return bean ...
    }
}
```



**@ComponentScan 配置其它组件扫描路径**

```java
@Configuration
@ComponentScan("com.acme.app.services")
public class AppConfig {
    // various @Bean definitions ...
}
```



**和 @Import 一起使用**

```java
   @Configuration
   public class DatabaseConfig {
  
       @Bean
       public DataSource dataSource() {
           // instantiate, configure and return DataSource
       }
   }
  
   @Configuration
   @Import(DatabaseConfig.class)
   public class AppConfig {
  
       private final DatabaseConfig dataConfig;
  
       public AppConfig(DatabaseConfig dataConfig) {
           this.dataConfig = dataConfig;
       }
  
       @Bean
       public MyBean myBean() {
           // reference the dataSource() bean method
           return new MyBean(dataConfig.dataSource());
       }
   }
```



**和 @Profile 一起使用**

```java
   @Profile("development")
   @Configuration
   public class EmbeddedDatabaseConfig {
  
       @Bean
       public DataSource dataSource() {
           // instantiate, configure and return embedded DataSource
       }
   }
  
   @Profile("production")
   @Configuration
   public class ProductionDatabaseConfig {
  
       @Bean
       public DataSource dataSource() {
           // instantiate, configure and return production DataSource
       }
   }

```



**@Profile + @Bean **

```java
   @Configuration
   public class ProfileDatabaseConfig {
  
       @Bean("dataSource")
       @Profile("development")
       public DataSource embeddedDatabase() { ... }
  
       @Bean("dataSource")
       @Profile("production")
       public DataSource productionDatabase() { ... }
   }
```



**嵌套使用**

```java
   @Configuration
   public class AppConfig {
  
       @Inject DataSource dataSource;
  
       @Bean
       public MyBean myBean() {
           return new MyBean(dataSource);
       }
  
       @Configuration
       static class DatabaseConfig {
           @Bean
           DataSource dataSource() {
               return new EmbeddedDatabaseBuilder().build();
           }
       }
   }
```



**启用 spring 支持的 @Enable 特性**

```java
@Configuration
@EnableAsync
@EnableScheduling
@EnableTransactionManagement
@EnableAspectJAutoProxy
@EnableWebMvc
public class AppConfig{
    
    
}
```



## 1.1. 使用限制



contriants:

1. 必须标注在 class 上
2. 标注的 class 不能是 final
   - 如果标注的 class 必须是final ，设置`proxyBeanMethods=false`，这种情况下，该class不会被增强
3. 嵌套的 @Configuration 标注的类，必须是 static



## 1.2. 源码分析

- 处理 @Configuation 需要的组件
  1. `org.springframework.context.annotation.ConfigurationClassPostProcessor`
  2. `org.springframework.context.annotation.ConfigurationClassParser`
  3. `org.springframework.context.annotation.ConfigurationClassBeanDefinitionReader`
- 用到的工具：`ConfigurationClassUtils`
- 







# 2. @Import













# 3. @Conditional



- 注解定义：

  ```java
  @Target({ElementType.TYPE, ElementType.METHOD})
  @Retention(RetentionPolicy.RUNTIME)
  @Documented
  public @interface Conditional {
  
  	
  	Class<? extends Condition>[] value();
  
  }
  ```

- 特点
  - 这个注解标注在类 -> 那么该组件想要注册到 IOC 容器，必须满足 condition
  - 这个可以标注在其它注解上 -> 衍生注解





## 3.1. 检查 - `Condition`



`org.springframework.context.annotation.Condition`

- 接口
- 重要 API，`boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata);`
- 匹配结果：`org.springframework.boot.autoconfigure.condition.ConditionOutcome`
  - 属性：
    1. `boolean match`, 是否匹配
    2. `ConditionMessage message`，条件信息





## 3.2. @ConditionalOnClass

- 作用
  - 某些类存在的时候，才会创建该bean

- 如何判断某个 class 是否存在
  - Class.forName(className) 方法







## 3.3. @ConditionalOnProperty



Environment 中是否存在某个属性，判断 condition 是否匹配

- Environment 可以由外部化配置文件（properties，yaml）,命令行组成



- 属性

  - `value`
    - 要在 Environment 中查找的值
  - `prefix`
  - `name`
    - 如果 prefix = app.config，name=my-value，则完整的 key = app.config.my-value
  - `havingValue`
  - `matchIfMissing`
    - true，没有这个值，算作 match
    - false，Environment 中没有这个值，算作不匹配

  

  

  

  

# 4. @AutoConfigureBefore



只是影响 bean 的定义顺序，并不会影响 bean 的创建顺序







