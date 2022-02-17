# 重新认识 IOC





# Spring IOC 概述





# Spring Bean

## 基础



## Bean 作用域



## Bean 生命周期





# Spring 依赖查找





# Spring 依赖注入



## 注入方式



![image-20220210175232082](https://typora-covey.oss-cn-shanghai.aliyuncs.com/img/image-20220210175232082.png)



## 注入类型

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







## 延迟注入







## 限定注入 @Qualifier

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







## @Autowired 注入原理



## 自定义依赖注入注解









# IOC 依赖来源

