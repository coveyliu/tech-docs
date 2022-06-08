<!-- TOC -->

- [1. 重新认识 IOC](#1-%E9%87%8D%E6%96%B0%E8%AE%A4%E8%AF%86-ioc)
- [2. Spring IOC 概述](#2-spring-ioc-%E6%A6%82%E8%BF%B0)
- [3. Spring Bean](#3-spring-bean)
    - [3.1. 基础](#31-%E5%9F%BA%E7%A1%80)
    - [3.2. Bean 作用域](#32-bean-%E4%BD%9C%E7%94%A8%E5%9F%9F)
    - [3.3. Bean 生命周期](#33-bean-%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F)
- [4. Spring 依赖查找](#4-spring-%E4%BE%9D%E8%B5%96%E6%9F%A5%E6%89%BE)
- [5. Spring 依赖注入](#5-spring-%E4%BE%9D%E8%B5%96%E6%B3%A8%E5%85%A5)
    - [5.1. 注入方式](#51-%E6%B3%A8%E5%85%A5%E6%96%B9%E5%BC%8F)
    - [5.2. 注入类型](#52-%E6%B3%A8%E5%85%A5%E7%B1%BB%E5%9E%8B)
    - [5.3. 延迟注入](#53-%E5%BB%B6%E8%BF%9F%E6%B3%A8%E5%85%A5)
    - [5.4. 限定注入 @Qualifier](#54-%E9%99%90%E5%AE%9A%E6%B3%A8%E5%85%A5-qualifier)
    - [5.5. @Autowired 注入原理](#55-autowired-%E6%B3%A8%E5%85%A5%E5%8E%9F%E7%90%86)
    - [5.6. 自定义依赖注入注解](#56-%E8%87%AA%E5%AE%9A%E4%B9%89%E4%BE%9D%E8%B5%96%E6%B3%A8%E5%85%A5%E6%B3%A8%E8%A7%A3)
- [6. IOC 依赖来源](#6-ioc-%E4%BE%9D%E8%B5%96%E6%9D%A5%E6%BA%90)

<!-- /TOC -->

# 1. 重新认识 IOC





# 2. Spring IOC 概述





# 3. Spring Bean

## 3.1. 基础



## 3.2. Bean 作用域



## 3.3. Bean 生命周期





# 4. Spring 依赖查找





# 5. Spring 依赖注入



## 5.1. 注入方式



![image-20220210175232082](https://typora-covey.oss-cn-shanghai.aliyuncs.com/img/image-20220210175232082.png)



## 5.2. 注入类型

- 基本类型
- 引用类型
- 集合类型



==集合类型注入，也是可以的，多用用吧==

```java
public class Demo{ 
    @Autowired
    Collection<User> users;

    @Autowired
    Map<String, User> userMap;
}
```







## 5.3. 延迟注入







## 5.4. 限定注入 @Qualifier

三个作用：

1. 限定注入
2. 分组
3. 自定义分组



==1、准备两个 POJO==

```java
public class User{

    private Long id;

}

public class SuperUser extends User {
    
    private String address;
}
```

==2、xml 配置文件==

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans
        xmlns="http://www.springframework.org/schema/beans"
        xmlns:context="http://www.springframework.org/schema/context"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">
    
    <bean id="user" class="org.geekbang.thinking.in.spring.ioc.overview.domain.User">
        <property name="id" value="1"/>
    </bean>

    <bean id="superUser" class="org.geekbang.thinking.in.spring.ioc.overview.domain.SuperUser" parent="user"
          primary="true">
        <property name="address" value="杭州"/>
    </bean>
</beans>
```



==3、测试==

```java
@Configuration
public class QualifierAnnotationDependencyInjectionDemo {

    @Autowired
    private User user; // superUser -> primary =true

    @Autowired
    @Qualifier("user") // 指定 Bean 名称或 ID
    private User namedUser;

    @Autowired
    private Collection<User> allUsers; // 2 Beans = user + superUser

    @Autowired
    @Qualifier
    private Collection<User> qualifiedUsers; // 

    @Autowired
    @UserGroup
    private Collection<User> groupedUsers; // 2 Beans = user3 + user4

    @Bean
    @Qualifier // 进行逻辑分组
    public User user1() {
        return createUser(7L);
    }

    @Bean
    @Qualifier // 进行逻辑分组
    public static User user2() {
        return createUser(8L);

    }

    @Bean
    @UserGroup
    public static User user3() {
        return createUser(9L);
    }

    @Bean
    @UserGroup
    public static User user4() {
        return createUser(10L);
    }

    private static User createUser(Long id) {
        User user = new User();
        user.setId(id);
        return user;
    }

    public static void main(String[] args) {

        // 创建 BeanFactory 容器
        AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext();
        // 注册 Configuration Class（配置类） -> Spring Bean
        applicationContext.register(QualifierAnnotationDependencyInjectionDemo.class);

        XmlBeanDefinitionReader beanDefinitionReader = new XmlBeanDefinitionReader(applicationContext);

        String xmlResourcePath = "classpath:/META-INF/beanDefinition.xml"; // 读取第二步的 xml 配置文件
        // 加载 XML 资源，解析并且生成 BeanDefinition
        beanDefinitionReader.loadBeanDefinitions(xmlResourcePath);

        // 启动 Spring 应用上下文
        applicationContext.refresh();

        // 依赖查找 QualifierAnnotationDependencyInjectionDemo Bean
        QualifierAnnotationDependencyInjectionDemo demo = applicationContext.getBean(QualifierAnnotationDependencyInjectionDemo.class);

        // 期待输出 superUser Bean
        System.out.println("demo.user = " + demo.user); // demo.user = SuperUser{address='杭州'} User{id=1}}
        // 期待输出 user Bean
        System.out.println("demo.namedUser = " + demo.namedUser); // User{id=1}
        // 期待输出 superUser user user1 user2
        System.out.println("demo.allUsers: ");
        demo.allUsers.forEach(System.out::println);
         // SuperUser{address='杭州'} User{id=1}}
         // User{id=1}
         // User{id=8}
         // User{id=9}
         // User{id=10}
        
        // 期待输出 user1 user2
        System.out.println("demo.qualifiedUsers: ");
        demo.qualifiedUsers.forEach(System.out::println);
	     // User{id=8}
         // User{id=9}
         // User{id=10}

        // 期待输出 user3 user4
        System.out.println("demo.groupedUsers: ");
        demo.groupedUsers.forEach(System.out::println);
         // User{id=9}
         // User{id=10}


        // 显示地关闭 Spring 应用上下文
        applicationContext.close();
    }

```







## 5.5. @Autowired 注入原理



## 5.6. 自定义依赖注入注解









# 6. IOC 依赖来源

