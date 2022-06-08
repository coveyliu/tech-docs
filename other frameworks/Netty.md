# 重要类

## `NioEventLoopGroup`





## `ServerSocketChannel`







## `ChannelHandler `& `ChannelPipeline`

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

  



## `ByteBuf`



