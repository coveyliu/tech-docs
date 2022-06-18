<!-- TOC -->

- [1. Channel](#1-channel)
    - [1.1. 状态模型](#11-%E7%8A%B6%E6%80%81%E6%A8%A1%E5%9E%8B)
    - [1.2. API](#12-api)
    - [1.3. pipeline](#13-pipeline)
- [2. ChannelHandler](#2-channelhandler)
    - [2.1. 触发生命周期回调](#21-%E8%A7%A6%E5%8F%91%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E5%9B%9E%E8%B0%83)
- [3. 编解码](#3-%E7%BC%96%E8%A7%A3%E7%A0%81)
    - [3.1. 两个抽象](#31-%E4%B8%A4%E4%B8%AA%E6%8A%BD%E8%B1%A1)
    - [3.2. netty-codec](#32-netty-codec)
- [4. 应用](#4-%E5%BA%94%E7%94%A8)
    - [4.1. idle 检测](#41-idle-%E6%A3%80%E6%B5%8B)
    - [4.2. 安全](#42-%E5%AE%89%E5%85%A8)
- [5. 重要类](#5-%E9%87%8D%E8%A6%81%E7%B1%BB)
    - [5.1. NioEventLoopGroup](#51-nioeventloopgroup)
    - [5.2. ServerSocketChannel](#52-serversocketchannel)
    - [5.3. ChannelHandler & ChannelPipeline](#53-channelhandler--channelpipeline)
    - [5.4. ByteBuf](#54-bytebuf)
- [6. 参考文档](#6-%E5%8F%82%E8%80%83%E6%96%87%E6%A1%A3)

<!-- /TOC -->

# 1. `Channel`



## 1.1. 状态模型

`channel` 是有状态的。`netty 3.x` 版本中 `channel` 的状态和 `netty 4.x` 版本的有些不一样。总的来说，`netty 4.x`  简化了 `channel` 状态。

`netty 4.x` 版本为例，`channel` 状态有：`registered`，`unRegistered`, `active`,` inActive`。其中，`registered`, `unregistered` 是新增的，`active`, `inactive` 是由 `3.x` 版本的其它状态合并过来的。nnetty 提供`active`, `inactive` 状态是告诉用户` channel` 已经连接好了，你可以 `read` 或 `write` 

至于 `netty 3.x` 版本中 `channel` 状态，可以参考其它文档



**3.x VS 4.0**

![image-20220618161102319](https://typora-covey.oss-cn-shanghai.aliyuncs.com/img/image-20220618161102319.png)



## 1.2. API



可以分两类，1：获取组件， 2. 传播生命周期事件



```
1. 获取组件
    - Channel channel();
    - EventExecutor executor();
    - ChannelHandler handler();
    - ChannelPipeline pipeline();
    - ByteBufAllocator alloc();
2. 传播生命周期事件
	- 
```





## 1.3. `pipeline`

主要内容：

1. `channel`, `pipeline` ,`ChannelHandler`, `ChannelHandlerContext` 间的关系
2. `ChannelPipeline` 的 API
3. `handler` 链处理顺序？
4. 在 `pipeline`上，如何中止某一站处理操作？
5. `pipeline` 上的 `handler`，如何热插拔？





- 每个 `channel` 都有一个`pipeline`。(获取方法：`io.netty.channel.Channel#pipeline`)

- `pipeline` 的每个节点是 `ChannelHandlerContext`。（可以在 DefaultChannelPipeline 中看到）

- `ChannelPipeline` API ，分类。

  两类：1. 获取组件， 2. 传播生命周期事件，3. 添加 handler

  ```
  1. 获取组件
      - Channel channel();
      - EventExecutor executor();
      - ChannelHandler handler();
      - ChannelPipeline pipeline();
      - ByteBufAllocator alloc();
  2. 传播事件（fireXX)
  	- ChannelPipeline fireChannelRegistered();
  	- ChannelPipeline fireChannelUnregistered();
  	- ChannelPipeline fireChannelActive();
  	- ChannelPipeline fireChannelInactive();
  	- ChannelPipeline fireExceptionCaught(Throwable cause);
  	- ChannelPipeline fireUserEventTriggered(Object event);
  	- ChannelPipeline fireChannelRead(Object msg);
  	- ChannelPipeline fireChannelReadComplete();
  	- ChannelPipeline fireChannelWritabilityChanged();
  3. 添加 handler
  	- ChannelPipeline addLast(ChannelHandler... handlers);
  	- ChannelPipeline addLast(String name, ChannelHandler handler);
  	- 其它省略 ... (上面两个最常用)
  ```

- handler 处理顺序
   - 入站

       pipeLine.addLast(handlerA), pipeLine.addLast(handlerB),pipeLine.addLast(handlerC)。处理顺序：A -> B ->C

  - 出站

       pipeLine.addLast(handlerA), pipeLine.addLast(handlerB),pipeLine.addLast(handlerC)。处理顺序：C -> B ->A

- 在某一站中止处理

  - 入站

    ​	如果 handler 是 extends ChannelInboundHandler	

  - 出站
          出站操作是不能被截断的

- handler 热插拔
  	添加：pipeLine 中的添加方法很多。并且，使用者一般都对添加方法熟悉，这里不多说了

  ​	删除：pipeLine 提供了 remove 方法，可以删除 handler

  ​	

​	

# 2. `ChannelHandler`



## 2.1. 触发生命周期回调



- `public interface ChannelInboundHandler extends ChannelHandler`

  - void channelRegistered(ChannelHandlerContext ctx)

    channel 注册成功后，触发。在这个操作中，你可以选择是否让 netty 触发 fireChannelRead，如果触发，则channel  流水线（pipeline）上的所有 handler 的 channelRegistered 方法都会被触发。 其它生命周期方法也有对应的 fireChannelXXX 方法

  - void channelUnregistered(ChannelHandlerContext ctx)

  - void channelActive(ChannelHandlerContext ctx) 

  - void channelInactive(ChannelHandlerContext ctx)

  - void channelRead(ChannelHandlerContext ctx, Object msg)

  - void channelReadComplete(ChannelHandlerContext ctx)

  - void userEventTriggered(ChannelHandlerContext ctx, Object evt)

- `public interface ChannelOutboundHandler extends ChannelHandler`
		
	- void bind(ChannelHandlerContext ctx, SocketAddress localAddress, ChannelPromise promise)
	
	   socket 和 ip+port 绑定，用于服务端
	
	- void connect(ChannelHandlerContext ctx, SocketAddress remoteAddress, SocketAddress localAddress, ChannelPromise promise)
	
	- void disconnect(ChannelHandlerContext ctx, ChannelPromise promise)
	
	- void close(ChannelHandlerContext ctx, ChannelPromise promise)
	
	- void deregister(ChannelHandlerContext ctx, ChannelPromise promise)
	
	- void read(ChannelHandlerContext ctx)。（OutboundHandler 的 read 和 write 方法不是很清楚）
	
	- void write(ChannelHandlerContext ctx, Object msg, ChannelPromise promise)。（OutboundHandler 的 read 和 write 方法不是很清楚）
	
	- void flush(ChannelHandlerContext ctx)。（将底层缓冲区中的数据发送给对端）





# 3. 编解码



- 一条消息从 `Bytebuf` 获取开始，到最终使用可能经过多次编码（`Bytebuf -> POJO1 -> POJO2 -> ...`)。同样，一个 `java POJO` 到最终出站，也会经过多次解码

- 什么叫一次解码器，什么叫二次解码器？（一次编码器，二次编码器同理，这里省略 ）
  - 一次解码器：将 `Bytebuf` 中的字节数据解码为 `java POJO`。通常是 `ByteToMessageDecoder` 的子类
  - 二次解码器：将 `java POJO` 编码为另一个 `POJO` 。通常是 `MessageToMessageDecoder`的子类



## 3.1. 两个抽象





## 3.2. netty-codec



- netty 中最多的子模块就是 `codec-xxx`。比如：`codec-http`, `codec-http2`, `codec-xml`, `codec-redis`。说明：
     - 这些常用的协议，netty 帮你了，开发者不必关注这些协议了		
     - 协议解析工作也是使用 netty 时，一个重要的工作

- 为什么 codec 包下的接口少？

  - codec 下的都是 `encoder` 和 `decoder`，而 `encoder` 和 `decoder` 的顶层接口是：`ChannelHandler`，`ChannelInboundHandler`， `ChannelOutboundHandler`

  - `decoder`，`encoder` 基本上是 `ByteToMessageDecoder`, `MessageToMessageDecoder`这两个抽象类的实现





# 4. 应用



## 4.1. idle 检测



## 4.2. 安全





# 5. 重要类



## 5.1. `NioEventLoopGroup`





## 5.2. `ServerSocketChannel`







## 5.3. `ChannelHandler `& `ChannelPipeline`

```xml
- ChannelHandler 分类？
- InboundHandler 和 OutboundHandler 分别什么时候工作？
- 解码器（encoder）和编码器（decoder)分别是哪种类型的 ChannelHandler
- ChannelPipeline 是如何组织 ChannelHandler 的？
```





- `ChannleHandler` 

  `ChannelHandler` 的主要作用就是用来处理 `I/O` 事件的，分为出站和入站处理器。`Encoder` 是 `ChannlerInboundHandler` 的一种，作用是将字节转为 `java`对象，`Decoder` 是 `ChannelOutboundHandler` 的一种，作用是将 `java` 对象转为 `byte` 传输出去 

<img width = '80%' src="https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220605210043.png" alt="image-20220605210042835" />



- `ChannelPipeline`

  **示意图**

  ```
  *  +---------------------------------------------------+---------------+
  *  |                           ChannelPipeline         |               |
  *  |                                                  \|/              |
  *  |    +---------------------+            +-----------+----------+    |
  *  |    | Inbound Handler  N  |            | Outbound Handler  1  |    |
  *  |    +----------+----------+            +-----------+----------+    |
  *  |              /|\                                  |               |
  *  |               |                                  \|/              |
  *  |    +----------+----------+            +-----------+----------+    |
  *  |    | Inbound Handler N-1 |            | Outbound Handler  2  |    |
  *  |    +----------+----------+            +-----------+----------+    |
  *  |              /|\                                  .               |
  *  |               .                                   .               |
  *  | ChannelHandlerContext.fireIN_EVT() ChannelHandlerContext.OUT_EVT()|
  *  |        [ method call]                       [method call]         |
  *  |               .                                   .               |
  *  |               .                                  \|/              |
  *  |    +----------+----------+            +-----------+----------+    |
  *  |    | Inbound Handler  2  |            | Outbound Handler M-1 |    |
  *  |    +----------+----------+            +-----------+----------+    |
  *  |              /|\                                  |               |
  *  |               |                                  \|/              |
  *  |    +----------+----------+            +-----------+----------+    |
  *  |    | Inbound Handler  1  |            | Outbound Handler  M  |    |
  *  |    +----------+----------+            +-----------+----------+    |
  *  |              /|\                                  |               |
  *  +---------------+-----------------------------------+---------------+
  *                  |                                  \|/
  *  +---------------+-----------------------------------+---------------+
  *  |               |                                   |               |
  *  |       [ Socket.read() ]                    [ Socket.write() ]     |
  *  |                                                                   |
  *  |  Netty Internal I/O Threads (Transport Implementation)            |
  *  +-------------------------------------------------------------------+
  ```

  **主要方法**

  



## 5.4. `ByteBuf`







# 6. 参考文档

>1. [netty 4.0 新特性](http://ifeve.com/netty-4-0-new/#more-32469) 
