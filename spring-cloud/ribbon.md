# 目标



> 1. 理解客户端负载均衡和服务端负载均衡
> 2. 理解 ribbon 调用原理，（负载均衡算法、自定义负载均衡）
> 3. ribbon 源码分析
>    - `IRule`。（负载均衡规则）
>    - 





# 负载均衡



## 分类



服务端负载均衡：`nginx`

客户端负载均衡：`ribbon`





## 负载均衡算法











# 关于 Ribbon



`spring cloud` 项目中，有两家公司提供了很多组件，包括 **alibaba** 和 **netfix**

**netfilx** 公司提供了：

- `Eureka`
- `Ribbon`
- `Hystrix`







## 在微服务架构中做了什么？







## 整合







## 配置

















# Ribbon 源码



## 相关接口



参考: `org.springframework.cloud.netflix.ribbon.RibbonClientConfiguration`

- `IClientConfig` ：`Ribbon` 的客户端配置，默认：`DefaultClientConfigImpl`
- `IRule`：负载均衡策略，默认：`ZoneAvoidanceRule`，该策略能够在多区域环境下选出最佳区域的实例进行访问
- `IPing`：`Ribbon` 的实例检查策略，默认：`DummyPing` 该策略始终返回 `true`，默认所有实例均可用
- `ServerList`: 服务实例的清单维护机制，默认：`ConfigurationBasedServerList`
- `ServerListFilter`：服务实例清单的过滤机制，默认采用：`ZonePreferenceServerListFilter`
- `ILoadBalancer`：负载均衡器，默认实现：`ZoneAwareLoadBalancer`，它具备区域感知的能力

