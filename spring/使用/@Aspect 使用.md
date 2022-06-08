# 1. @Aspect 支持

<!-- TOC -->

- [1. @Aspect 支持](#1-aspect-%E6%94%AF%E6%8C%81)
    - [1.1. 启用 @Aspect](#11-%E5%90%AF%E7%94%A8-aspect)
    - [1.2. 声明一个切面](#12-%E5%A3%B0%E6%98%8E%E4%B8%80%E4%B8%AA%E5%88%87%E9%9D%A2)
    - [1.3. 声明 Pointcut](#13-%E5%A3%B0%E6%98%8E-pointcut)
    - [1.4. 共享 pointcut](#14-%E5%85%B1%E4%BA%AB-pointcut)
    - [1.5. pointcut 表达式例子](#15-pointcut-%E8%A1%A8%E8%BE%BE%E5%BC%8F%E4%BE%8B%E5%AD%90)
    - [1.6. Advice](#16-advice)
        - [1.6.1. advice 参数](#161-advice-%E5%8F%82%E6%95%B0)

<!-- /TOC -->




`@Aspect` 能将一个 class 声明为一个切面 `Aspect`，这是`Asepct` 的一种风格，spring AOP 同样支持这个风格

Spring 还是利用的 `Aspect` 的相关的 library 解析、匹配 `Pointcut`

- 解析：`@Pointcut` 注解的内容
- 匹配：应用  `Pointcut`



## 1.1. 启用 @Aspect



-  spring AOP 基于代理模式
- 对于需要代理的 Spring bean（满足 `Pointcut` )，spring 会自动为其生成一个代理类，以实现方法拦截，执行 `Advice`



如何启用 `@Aspect` 注解支持？

1. java 编程方式：

   `@EnableAspectJAutoProxy`

   ```java
   @Configuration
   @EnableAspectJAutoProxy
   public class AppConfig {
   
   }
   ```

   

2. xml 配置方式

   ```xml
   <aop:aspectj-autoproxy/>
   ```

注意：上述两种方式，都需要在项目中引入 `Aspect` 的 `aspectjweaver.jar`





## 1.2. 声明一个切面



两种方式：

- xml 配置
- java 编程方式



这里仅讨论 `java` 编程方式

1. 写一个普通类
2. 在类上标注 `Aspect`
3. 在类上标注：`Component` 等注解，让该类能被 spring IOC 容器扫描到

- 单独使用 `Aspect` 是不能被 spring 扫描的



## 1.3. 声明 Pointcut



- spring 仅支持方法级别的拦截。因此，声明 Pointcut 时，里面的描述内容应该是匹配的方法

- Pointcut 包含两部分：签名 signature + 表达式 pointcut expression

  ```
  @Pointcut("execution(* transfer(..))") // the pointcut expression
  private void anyOldTransfer() {} // the pointcut signature
  ```



关于 pointcut expression

- 这是 Aspect 的 pointcut expression，spring 现在是拿来用

- spring 仅支持部分的 pointcut 标识符

  - `execution`

    - 方法匹配
  
    - 使用方式：`execution(权限修饰符 返回值类型 )
  
    - ```
      execution(modifiers-pattern? ret-type-pattern declaring-type-pattern?name-pattern(param-pattern)
                      throws-pattern?)
      ```
  
    - 比如：`**@Pointcut**("execution(public * *(..))")` 匹配任何 public 方法
  
  - `within`
  
    - 在某个范围内的对象，会被代理
    - 比如：`**@Pointcut**("within(com.xyz.myapp.trading..*)")` 在 trading 包内

  - `this`

  - `target`

  - `args`

    - 参数，必须是给定类型

  - `@target`

  - `@args`

  - `@within`
  
  - `@annotation`
  
    - 包含给定的注解

  - `bean`
  
    - spring 对 Aspect 的扩展，可以仅代理特定的 spring bean
    - 这是一种实例级别的筛选，和 spring 更加密切
    - 使用：
      - 通配符：*
      - &&，||, !
  





## 1.4. 共享 pointcut



有些 pointcut 表达式能被复用



可以建立一个通用的切面，然后再引入

```java
package cn.aspect.demo

@Aspect
public class CommonPointcuts {

    /**
     * A join point is in the web layer if the method is defined
     * in a type in the com.xyz.myapp.web package or any sub-package
     * under that.
     */
    @Pointcut("within(com.xyz.myapp.web..*)")
    public void inWebLayer() {}

    /**
     * A join point is in the service layer if the method is defined
     * in a type in the com.xyz.myapp.service package or any sub-package
     * under that.
     */
    @Pointcut("within(com.xyz.myapp.service..*)")
    public void inServiceLayer() {}

    /**
     * A join point is in the data access layer if the method is defined
     * in a type in the com.xyz.myapp.dao package or any sub-package
     * under that.
     */
    @Pointcut("within(com.xyz.myapp.dao..*)")
    public void inDataAccessLayer() {}

    /**
     * A business service is the execution of any method defined on a service
     * interface. This definition assumes that interfaces are placed in the
     * "service" package, and that implementation types are in sub-packages.
     *
     * If you group service interfaces by functional area (for example,
     * in packages com.xyz.myapp.abc.service and com.xyz.myapp.def.service) then
     * the pointcut expression "execution(* com.xyz.myapp..service.*.*(..))"
     * could be used instead.
     *
     * Alternatively, you can write the expression using the 'bean'
     * PCD, like so "bean(*Service)". (This assumes that you have
     * named your Spring service beans in a consistent fashion.)
     */
    @Pointcut("execution(* com.xyz.myapp..service.*.*(..))")
    public void businessService() {}

    /**
     * A data access operation is the execution of any method defined on a
     * dao interface. This definition assumes that interfaces are placed in the
     * "dao" package, and that implementation types are in sub-packages.
     */
    @Pointcut("execution(* com.xyz.myapp.dao.*.*(..))")
    public void dataAccessOperation() {}

}
```





```java
@Component
@Aspect
public MyAspectConfiguration{
    
    @Before("cn.aspect.demo.CommonPointcuts.inWebLayer()")
    public void before(){
        System.out.println("[ before ]")
    }
}
```









## 1.5. pointcut 表达式例子



>  参考官网 
>
> https://docs.spring.io/spring-framework/docs/5.2.19.RELEASE/spring-framework-reference/core.html#aop-pointcuts-examples





## 1.6. Advice



### 1.6.1. advice 参数



- 任意的 advice 都可以接受一个 `JointPoint` ，并且是第一个参数
- AroundAdvice 的第一个参数必须是：`ProceedingJoinPoint`
- `JointPoint` API
  - `getArgs()`: Returns the method arguments.
  - `getThis()`: Returns the proxy object.
  - `getTarget()`: Returns the target object.
  - `getSignature()`: Returns a description of the method that is being advised.
  - `toString()`: Prints a useful description of the method being advised.