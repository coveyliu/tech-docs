# 服务雪崩



如图，服务 A 有 30 个线程，正常情况下，调用服务 B, C, D 都是可以调通的

![image-20220220184551009](https://typora-covey.oss-cn-shanghai.aliyuncs.com/img/image-20220220184551009.png)



现在，服务 A 的 30 个线程都用来调用服务D，但是，服务D 不可用，这 30 个线程被占满了。服务 A 再调用服务B,C 时，也调不通了，服务 A 变得不可用了 ！！！

这是多么扯淡的事情，下游服务不可用，导致整个服务不可用，那还写什么微服务啊

![image-20220220185026314](https://typora-covey.oss-cn-shanghai.aliyuncs.com/img/image-20220220185026314.png)



上面就是==服务雪崩==的过程





# 解决办法



1. 超时配置

2. 限流

3. 熔断。（发现服务不可用时，直接使用降级接口返回数据给调用方）
   - 

4. 降级。（走了降级接口`fallback` 返回了数据）



# Hystrix





## 引入

当时，写这个时候，`Hystrix`版本是`2.2.9.RELEASE`

文档：https://docs.spring.io/spring-cloud-netflix/docs/2.2.10.RELEASE/reference/html/



```xml
 <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
        <version>2.2.9.RELEASE</version>
      </dependency>
```









## 配置



详细的配置参数，可以查看官网的 `wiki`:https://github.com/Netflix/Hystrix/wiki/Configuration

![image-20220220154528017](https://typora-covey.oss-cn-shanghai.aliyuncs.com/img/image-20220220154528017.png)











## 断路器















![image-20220220164657477](https://typora-covey.oss-cn-shanghai.aliyuncs.com/img/image-20220220164657477.png)







## 限流配置

1. 配置线程池



![image-20220220163040663](https://typora-covey.oss-cn-shanghai.aliyuncs.com/img/image-20220220163040663.png)



![image-20220220163102040](https://typora-covey.oss-cn-shanghai.aliyuncs.com/img/image-20220220163102040.png)





## Hystrix 超时 vs Ribbon 超时



如果，在使用 `Ribbon` 的时候，使用了 `Hystrix` 那么在设置 `Hystrix`的超时时间时，必须考虑 `Ribbon`的重试。

**官网的例子：**

​	*如果 Ribbon 连接超时时间为1秒，Ribbon 客户机可能重试请求3次，那么 Hystrix 超时时间应该略多于3秒。*





## dashboard

