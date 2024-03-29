# 基础

```
1. JRE 和 jdk 的区别
2. 你是怎么理解方法区的？
3. 线程私有的内存空间包含什么？
```



JRE，全称：java 运行时环境，包括：java 虚拟机 + 核心类库

JDK = JRE + 开发、诊断工具



可以理解为：一个 `class` 的**全部代码**被放置在了方法区中

![image-20220820153026653](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20220820153026653.png)

线程私有的空间区域包括什么？

![image-20220820153640464](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20220820153640464.png)



# 虚拟机是如何运行 java 代码的？



```xml
- java 代码运行之前需要被加载到方法区中
- 虚拟机需要将字节码转为机器码，交给底层硬件执行。（这里是关键）
- 字节码 -> 机器码，两种方式：解释执行、JIT
- JIT 已方法为单位编译，你还需要知道，一个方法是如何从解释执行转为 JIT 的
```



![image-20220820154924319](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20220820154924319.png)



一般是先解释执行字节码，而后将其中反复执行的热点代码，以方法为单位进行即时编译。之后，再次运行该方法时，会使用即时编译后的机器码





# java 虚拟机的运行效率



```
- 不同场景下的 java 软件需要配置合适的编译器
```



java 提供了一些编译器：

- `c1` ，称为 client 编译器，采取静态编译，适用有启动要求的 GUI 客户端，比如: IDEA
- `c2` ,  Server 编译器，面向的是对峰值性能有要求的服务器端程序，采用的优化手段
  相对复杂，因此编译时间较长，但同时生成代码的执行效率较高。
- `Graal`,  是 Java 10 正式引入的实验性即时编译器



从 Java 7 开始，HotSpot 默认采用分层编译的方式：热点方法首先会被 C1 编译，而后热点方法中的热点会进一步被 C2 编译。



`c1`, `c2` 有不同的线程数目：

- HotSpot 会根据 CPU 的数量设置编译线程的数目，并且按 1:2 的比例配置给 C1 及 C2 编译器。