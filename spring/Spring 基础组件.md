# BeanDefinition

- 重要 API

  - 配置

    - `void setParentName(@Nullable String parentName);`
    - `void setBeanClassName(@Nullable String beanClassName);`
    - `void setScope(@Nullable String scope);`
    - `void setLazyInit(boolean lazyInit);`
    - `void setDependsOn(@Nullable String... dependsOn);`
    - `void setPrimary(boolean primary);`
    - `void setFactoryBeanName(@Nullable String factoryBeanName);`
    - `void setFactoryMethodName(@Nullable String factoryMethodName);`
    - `void setInitMethodName(@Nullable String initMethodName);`
    - `void setDestroyMethodName(@Nullable String destroyMethodName);`
    - `void setRole(int role);`

  - 判断

    - `boolean isLazyInit();`

    - `boolean isPrimary();`

    - `boolean isSingleton();`

    - `boolean isPrototype();`

    - `boolean isAbstract();`

      



- 类层次

  ![image-20220331103826243](https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220331103826.png)



## 实现



### `ScannedGenericBeanDefinition`











## holder - `BeanDefinitionHolder`



- 和 beanDefinition 的关系 -> 组合关系
  - 只有一种方式组合：构造函数
  - <img src="https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220331105120.png" alt="image-20220331105120011" style="zoom:50%;" />





## 生成 bean 名字 - `BeanNameGenerator`

- 作用：
  - 为 `BeanDefinition` 生成名字（beanName）

- 重要 API:

  - `String generateBeanName(BeanDefinition definition, BeanDefinitionRegistry registry);`

- 实现

  ![image-20220331105331003](https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220331105331.png)







# Resource



- [ ] 这部分还没做



## 获取 Resource - `ResourceLoader`



