<!-- TOC -->

- [1. Channel](#1-channel)
    - [1.1. 状态模型](#11-%E7%8A%B6%E6%80%81%E6%A8%A1%E5%9E%8B)
    - [1.2. API](#12-api)
    - [1.3. pipeline](#13-pipeline)
- [2. ChannelHandler](#2-channelhandler)
    - [2.1. 触发生命周期回调](#21-%E8%A7%A6%E5%8F%91%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E5%9B%9E%E8%B0%83)
    - [2.2. 编写业务handler](#22-%E7%BC%96%E5%86%99%E4%B8%9A%E5%8A%A1handler)
- [3. 编解码](#3-%E7%BC%96%E8%A7%A3%E7%A0%81)
    - [3.1. 封帧方式](#31-%E5%B0%81%E5%B8%A7%E6%96%B9%E5%BC%8F)
    - [3.2. 二次解码器](#32-%E4%BA%8C%E6%AC%A1%E8%A7%A3%E7%A0%81%E5%99%A8)
    - [3.3. 解码器](#33-%E8%A7%A3%E7%A0%81%E5%99%A8)
    - [3.4. netty-codec](#34-netty-codec)
- [4. 优化你的 netty 应用](#4-%E4%BC%98%E5%8C%96%E4%BD%A0%E7%9A%84-netty-%E5%BA%94%E7%94%A8)
    - [4.1. LoggingHandler](#41-logginghandler)
    - [4.2. idle 检测](#42-idle-%E6%A3%80%E6%B5%8B)
    - [4.3. 可视化](#43-%E5%8F%AF%E8%A7%86%E5%8C%96)
    - [4.4. 注解](#44-%E6%B3%A8%E8%A7%A3)
    - [4.5. flush 优化](#45-flush-%E4%BC%98%E5%8C%96)
    - [4.6. 防止内存泄漏](#46-%E9%98%B2%E6%AD%A2%E5%86%85%E5%AD%98%E6%B3%84%E6%BC%8F)
    - [4.7. 其它](#47-%E5%85%B6%E5%AE%83)
- [5. 重要类](#5-%E9%87%8D%E8%A6%81%E7%B1%BB)
    - [5.1. NioEventLoopGroup](#51-nioeventloopgroup)
    - [5.2. ServerSocketChannel](#52-serversocketchannel)
    - [5.3. ChannelHandler & ChannelPipeline](#53-channelhandler--channelpipeline)
    - [5.4. ByteBuf](#54-bytebuf)
- [6. 学习其它框架如何使用 Netty](#6-%E5%AD%A6%E4%B9%A0%E5%85%B6%E5%AE%83%E6%A1%86%E6%9E%B6%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8-netty)
    - [6.1. xxl-job](#61-xxl-job)
        - [6.1.1. HttpServerCodec](#611-httpservercodec)
        - [6.1.2. HttpObjectAggregator](#612-httpobjectaggregator)
- [7. 参考文档](#7-%E5%8F%82%E8%80%83%E6%96%87%E6%A1%A3)

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

```
1. `channel`, `pipeline` ,`ChannelHandler`, `ChannelHandlerContext` 间的关系
2. `ChannelPipeline` 的 API
3. `handler` 链处理顺序？
4. 在 `pipeline`上，如何中止某一站处理操作？
5. `pipeline` 上的 `handler`，如何热插拔？
```



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

- `handler` 处理顺序
  
   - 入站
   
       `pipeLine.addLast(handlerA)`, `pipeLine.addLast(handlerB)`,`pipeLine.addLast(handlerC)`。处理顺序：A -> B ->C
   
   - 出站
   
        `pipeLine.addLast(handlerA),` `pipeLine.addLast(handlerB)`,`pipeLine.addLast(handlerC)`。处理顺序：C -> B ->A
   
- 在某一站中止处理

  - 入站

    ​	如果 handler 是 extends ChannelInboundHandler	

  - 出站
          出站操作是不能被截断的

- `handler` 热插拔
  	添加：`pipeLine` 中的添加方法很多。并且，使用者一般都对添加方法熟悉，这里不多说了

  ​	删除：`pipeLine` 提供了 `remove` 方法，可以删除 handler

  ​	

​	

# 2. `ChannelHandler`

主要内容：

```
- ChannelHandler 分类？
- OutboundHandler，InboundHandler分别有哪些回调方法？这些方法是什么时候触发的？
- 业务开发时，如何编写自己的业务 handler ？
- SimpleHandlerAdapter 帮助业务开发业务人员都做了哪些事情？
```





## 2.1. 触发生命周期回调



- `public interface ChannelInboundHandler extends ChannelHandler`

  - `void channelRegistered(ChannelHandlerContext ctx)`

    channel 注册成功后，触发。在这个操作中，你可以选择是否让 netty 触发 fireChannelRead，如果触发，则channel  流水线（pipeline）上的所有 handler 的 channelRegistered 方法都会被触发。 其它生命周期方法也有对应的 fireChannelXXX 方法

  - `void channelUnregistered(ChannelHandlerContext ctx)`

  - `void channelActive(ChannelHandlerContext ctx)` 

  - `void channelInactive(ChannelHandlerContext ctx)`

  - `void channelRead(ChannelHandlerContext ctx, Object msg)`

  - `void channelReadComplete(ChannelHandlerContext ctx)`

  - `void userEventTriggered(ChannelHandlerContext ctx, Object evt)`

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





## 2.2. 编写业务`handler`

两种方式:

1. 继承 `ChannelInboundHandlerAdapter`
2. 继承 `SimpleChannelInboundHandler<T>`

对比：

1. 处理特定的消息用 `SimpleChannelInboundHandler<T>`



`ChannelInboundHandlerAdapter`

（注释重要）

```java
/**
 * Abstract base class for {@link ChannelInboundHandler} implementations which provide
 * implementations of all of their methods.
 *
 * This implementation just forward the operation to the next {@link ChannelHandler} in the
 * {@link ChannelPipeline}. Sub-classes may override a method implementation to change this.
 * Be aware that messages are not released after the {@link #channelRead(ChannelHandlerContext, Object)}
 * method returns automatically. If you are looking for a {@link ChannelInboundHandler} implementation that
 * releases the received messages automatically, please see {@link SimpleChannelInboundHandler}.
 */
public class ChannelInboundHandlerAdapter extends ChannelHandlerAdapter implements ChannelInboundHandler {
     @Skip
    @Override
    public void channelRegistered(ChannelHandlerContext ctx) throws Exception {
        ctx.fireChannelRegistered();
    }

    /**
     * Calls {@link ChannelHandlerContext#fireChannelUnregistered()} to forward
     * to the next {@link ChannelInboundHandler} in the {@link ChannelPipeline}.
     *
     * Sub-classes may override this method to change behavior.
     */
    @Skip
    @Override
    public void channelUnregistered(ChannelHandlerContext ctx) throws Exception {
        ctx.fireChannelUnregistered();
    }

    /**
     * Calls {@link ChannelHandlerContext#fireChannelActive()} to forward
     * to the next {@link ChannelInboundHandler} in the {@link ChannelPipeline}.
     *
     * Sub-classes may override this method to change behavior.
     */
    @Skip
    @Override
    public void channelActive(ChannelHandlerContext ctx) throws Exception {
        ctx.fireChannelActive();
    }

    /**
     * Calls {@link ChannelHandlerContext#fireChannelInactive()} to forward
     * to the next {@link ChannelInboundHandler} in the {@link ChannelPipeline}.
     *
     * Sub-classes may override this method to change behavior.
     */
    @Skip
    @Override
    public void channelInactive(ChannelHandlerContext ctx) throws Exception {
        ctx.fireChannelInactive();
    }

    /**
     * Calls {@link ChannelHandlerContext#fireChannelRead(Object)} to forward
     * to the next {@link ChannelInboundHandler} in the {@link ChannelPipeline}.
     *
     * Sub-classes may override this method to change behavior.
     */
    @Skip
    @Override
    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
        ctx.fireChannelRead(msg);
    }

    /**
     * Calls {@link ChannelHandlerContext#fireChannelReadComplete()} to forward
     * to the next {@link ChannelInboundHandler} in the {@link ChannelPipeline}.
     *
     * Sub-classes may override this method to change behavior.
     */
    @Skip
    @Override
    public void channelReadComplete(ChannelHandlerContext ctx) throws Exception {
        ctx.fireChannelReadComplete();
    }

    /**
     * Calls {@link ChannelHandlerContext#fireUserEventTriggered(Object)} to forward
     * to the next {@link ChannelInboundHandler} in the {@link ChannelPipeline}.
     *
     * Sub-classes may override this method to change behavior.
     */
    @Skip
    @Override
    public void userEventTriggered(ChannelHandlerContext ctx, Object evt) throws Exception {
        ctx.fireUserEventTriggered(evt);
    }

    /**
     * Calls {@link ChannelHandlerContext#fireChannelWritabilityChanged()} to forward
     * to the next {@link ChannelInboundHandler} in the {@link ChannelPipeline}.
     *
     * Sub-classes may override this method to change behavior.
     */
    @Skip
    @Override
    public void channelWritabilityChanged(ChannelHandlerContext ctx) throws Exception {
        ctx.fireChannelWritabilityChanged();
    }

    /**
     * Calls {@link ChannelHandlerContext#fireExceptionCaught(Throwable)} to forward
     * to the next {@link ChannelHandler} in the {@link ChannelPipeline}.
     *
     * Sub-classes may override this method to change behavior.
     */
    @Skip
    @Override
    @SuppressWarnings("deprecation")
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause)
            throws Exception {
        ctx.fireExceptionCaught(cause);
    }
}
```



`SimpleChannelInboundHandler`

(`channelRead` 之后，会释放)

```java
public abstract class SimpleChannelInboundHandler<I> extends ChannelInboundHandlerAdapter {

    private final TypeParameterMatcher matcher;
    private final boolean autoRelease;

    /**
     * see {@link #SimpleChannelInboundHandler(boolean)} with {@code true} as boolean parameter.
     */
    protected SimpleChannelInboundHandler() {
        this(true);
    }

    /**
     * Create a new instance which will try to detect the types to match out of the type parameter of the class.
     *
     * @param autoRelease   {@code true} if handled messages should be released automatically by passing them to
     *                      {@link ReferenceCountUtil#release(Object)}.
     */
    protected SimpleChannelInboundHandler(boolean autoRelease) {
        matcher = TypeParameterMatcher.find(this, SimpleChannelInboundHandler.class, "I");
        this.autoRelease = autoRelease;
    }

    /**
     * see {@link #SimpleChannelInboundHandler(Class, boolean)} with {@code true} as boolean value.
     */
    protected SimpleChannelInboundHandler(Class<? extends I> inboundMessageType) {
        this(inboundMessageType, true);
    }

    /**
     * Create a new instance
     *
     * @param inboundMessageType    The type of messages to match
     * @param autoRelease           {@code true} if handled messages should be released automatically by passing them to
     *                              {@link ReferenceCountUtil#release(Object)}.
     */
    protected SimpleChannelInboundHandler(Class<? extends I> inboundMessageType, boolean autoRelease) {
        matcher = TypeParameterMatcher.get(inboundMessageType);
        this.autoRelease = autoRelease;
    }

    /**
     * Returns {@code true} if the given message should be handled. If {@code false} it will be passed to the next
     * {@link ChannelInboundHandler} in the {@link ChannelPipeline}.
     */
    public boolean acceptInboundMessage(Object msg) throws Exception {
        return matcher.match(msg);
    }

    @Override
    public void 
        (ChannelHandlerContext ctx, Object msg) throws Exception {
        boolean release = true;
        try {
            if (acceptInboundMessage(msg)) {
                @SuppressWarnings("unchecked")
                I imsg = (I) msg;
                channelRead0(ctx, imsg);
            } else {
                release = false;
                ctx.fireChannelRead(msg);
            }
        } finally {
            if (autoRelease && release) {
                ReferenceCountUtil.release(msg);
            }
        }
    }

    /**
     * Is called for each message of type {@link I}.
     *
     * @param ctx           the {@link ChannelHandlerContext} which this {@link SimpleChannelInboundHandler}
     *                      belongs to
     * @param msg           the message to handle
     * @throws Exception    is thrown if an error occurred
     */
    protected abstract void channelRead0(ChannelHandlerContext ctx, I msg) throws Exception;
}
```



比如：处理 `String` 类型的消息：

```java
public class StringHandler extends SimpleChannelInboundHandler<String> {
  
    @Override
    protected void channelRead0(ChannelHandlerContext ctx, String message)
        throws Exception {
        System.out.println(message);
    }
}
```





# 3. 编解码

```
- 什么是一次解码器，什么是二次解码器？
- 两个重要的抽象 ByteToMessageCodec、MessageToMessageCodec
- 常用解码器：LengthFiledBasedDecoder
- netty-codec 提供了哪些常用的编解码器？
```



- 一条消息从 `Bytebuf` 获取开始，到最终使用可能经过多次编码（`Bytebuf -> POJO1 -> POJO2 -> ...`)。同样，一个 `java POJO` 到最终出站，也会经过多次解码

- 什么叫一次解码器，什么叫二次解码器？（一次编码器，二次编码器同理，这里省略 ）
  - 一次解码器：将 `Bytebuf` 中的字节数据解码为 `java POJO`。通常是 `ByteToMessageDecoder` 的子类
  - 二次解码器：将 `java POJO` 编码为另一个 `POJO` 。通常是 `MessageToMessageDecoder`的子类







## 封帧方式

```
- 三种封帧方式：FixedLengthFrameDecoder, DelimiterBasedFrameDecoder, LengthFiledBasedFraomDecoder
- LengthFiledBasedFrameDecoder 四大参数
- 
```



**`LengthFiledBasedFrameDecoder` 参数**

```
/**
 * A decoder that splits the received {@link ByteBuf}s dynamically by the
 * value of the length field in the message.  It is particularly useful when you
 * decode a binary message which has an integer header field that represents the
 * length of the message body or the whole message.
 * <p>
 * {@link LengthFieldBasedFrameDecoder} has many configuration parameters so
 * that it can decode any message with a length field, which is often seen in
 * proprietary client-server protocols. Here are some example that will give
 * you the basic idea on which option does what.
 *
 * <h3>2 bytes length field at offset 0, do not strip header</h3>
 *
 * The value of the length field in this example is <tt>12 (0x0C)</tt> which
 * represents the length of "HELLO, WORLD".  By default, the decoder assumes
 * that the length field represents the number of the bytes that follows the
 * length field.  Therefore, it can be decoded with the simplistic parameter
 * combination.
 * <pre>
 * <b>lengthFieldOffset</b>   = <b>0</b>
 * <b>lengthFieldLength</b>   = <b>2</b>
 * lengthAdjustment    = 0
 * initialBytesToStrip = 0 (= do not strip header)
 *
 * BEFORE DECODE (14 bytes)         AFTER DECODE (14 bytes)
 * +--------+----------------+      +--------+----------------+
 * | Length | Actual Content |----->| Length | Actual Content |
 * | 0x000C | "HELLO, WORLD" |      | 0x000C | "HELLO, WORLD" |
 * +--------+----------------+      +--------+----------------+
 * </pre>
 *
 * <h3>2 bytes length field at offset 0, strip header</h3>
 *
 * Because we can get the length of the content by calling
 * {@link ByteBuf#readableBytes()}, you might want to strip the length
 * field by specifying <tt>initialBytesToStrip</tt>.  In this example, we
 * specified <tt>2</tt>, that is same with the length of the length field, to
 * strip the first two bytes.
 * <pre>
 * lengthFieldOffset   = 0
 * lengthFieldLength   = 2
 * lengthAdjustment    = 0
 * <b>initialBytesToStrip</b> = <b>2</b> (= the length of the Length field)
 *
 * BEFORE DECODE (14 bytes)         AFTER DECODE (12 bytes)
 * +--------+----------------+      +----------------+
 * | Length | Actual Content |----->| Actual Content |
 * | 0x000C | "HELLO, WORLD" |      | "HELLO, WORLD" |
 * +--------+----------------+      +----------------+
 * </pre>
 *
 * <h3>2 bytes length field at offset 0, do not strip header, the length field
 *     represents the length of the whole message</h3>
 *
 * In most cases, the length field represents the length of the message body
 * only, as shown in the previous examples.  However, in some protocols, the
 * length field represents the length of the whole message, including the
 * message header.  In such a case, we specify a non-zero
 * <tt>lengthAdjustment</tt>.  Because the length value in this example message
 * is always greater than the body length by <tt>2</tt>, we specify <tt>-2</tt>
 * as <tt>lengthAdjustment</tt> for compensation.
 * <pre>
 * lengthFieldOffset   =  0
 * lengthFieldLength   =  2
 * <b>lengthAdjustment</b>    = <b>-2</b> (= the length of the Length field)
 * initialBytesToStrip =  0
 *
 * BEFORE DECODE (14 bytes)         AFTER DECODE (14 bytes)
 * +--------+----------------+      +--------+----------------+
 * | Length | Actual Content |----->| Length | Actual Content |
 * | 0x000E | "HELLO, WORLD" |      | 0x000E | "HELLO, WORLD" |
 * +--------+----------------+      +--------+----------------+
 * </pre>
 *
 * <h3>3 bytes length field at the end of 5 bytes header, do not strip header</h3>
 *
 * The following message is a simple variation of the first example.  An extra
 * header value is prepended to the message.  <tt>lengthAdjustment</tt> is zero
 * again because the decoder always takes the length of the prepended data into
 * account during frame length calculation.
 * <pre>
 * <b>lengthFieldOffset</b>   = <b>2</b> (= the length of Header 1)
 * <b>lengthFieldLength</b>   = <b>3</b>
 * lengthAdjustment    = 0
 * initialBytesToStrip = 0
 *
 * BEFORE DECODE (17 bytes)                      AFTER DECODE (17 bytes)
 * +----------+----------+----------------+      +----------+----------+----------------+
 * | Header 1 |  Length  | Actual Content |----->| Header 1 |  Length  | Actual Content |
 * |  0xCAFE  | 0x00000C | "HELLO, WORLD" |      |  0xCAFE  | 0x00000C | "HELLO, WORLD" |
 * +----------+----------+----------------+      +----------+----------+----------------+
 * </pre>
 *
 * <h3>3 bytes length field at the beginning of 5 bytes header, do not strip header</h3>
 *
 * This is an advanced example that shows the case where there is an extra
 * header between the length field and the message body.  You have to specify a
 * positive <tt>lengthAdjustment</tt> so that the decoder counts the extra
 * header into the frame length calculation.
 * <pre>
 * lengthFieldOffset   = 0
 * lengthFieldLength   = 3
 * <b>lengthAdjustment</b>    = <b>2</b> (= the length of Header 1)
 * initialBytesToStrip = 0
 *
 * BEFORE DECODE (17 bytes)                      AFTER DECODE (17 bytes)
 * +----------+----------+----------------+      +----------+----------+----------------+
 * |  Length  | Header 1 | Actual Content |----->|  Length  | Header 1 | Actual Content |
 * | 0x00000C |  0xCAFE  | "HELLO, WORLD" |      | 0x00000C |  0xCAFE  | "HELLO, WORLD" |
 * +----------+----------+----------------+      +----------+----------+----------------+
 * </pre>
 *
 * <h3>2 bytes length field at offset 1 in the middle of 4 bytes header,
 *     strip the first header field and the length field</h3>
 *
 * This is a combination of all the examples above.  There are the prepended
 * header before the length field and the extra header after the length field.
 * The prepended header affects the <tt>lengthFieldOffset</tt> and the extra
 * header affects the <tt>lengthAdjustment</tt>.  We also specified a non-zero
 * <tt>initialBytesToStrip</tt> to strip the length field and the prepended
 * header from the frame.  If you don't want to strip the prepended header, you
 * could specify <tt>0</tt> for <tt>initialBytesToSkip</tt>.
 * <pre>
 * lengthFieldOffset   = 1 (= the length of HDR1)
 * lengthFieldLength   = 2
 * <b>lengthAdjustment</b>    = <b>1</b> (= the length of HDR2)
 * <b>initialBytesToStrip</b> = <b>3</b> (= the length of HDR1 + LEN)
 *
 * BEFORE DECODE (16 bytes)                       AFTER DECODE (13 bytes)
 * +------+--------+------+----------------+      +------+----------------+
 * | HDR1 | Length | HDR2 | Actual Content |----->| HDR2 | Actual Content |
 * | 0xCA | 0x000C | 0xFE | "HELLO, WORLD" |      | 0xFE | "HELLO, WORLD" |
 * +------+--------+------+----------------+      +------+----------------+
 * </pre>
 *
 * <h3>2 bytes length field at offset 1 in the middle of 4 bytes header,
 *     strip the first header field and the length field, the length field
 *     represents the length of the whole message</h3>
 *
 * Let's give another twist to the previous example.  The only difference from
 * the previous example is that the length field represents the length of the
 * whole message instead of the message body, just like the third example.
 * We have to count the length of HDR1 and Length into <tt>lengthAdjustment</tt>.
 * Please note that we don't need to take the length of HDR2 into account
 * because the length field already includes the whole header length.
 * <pre>
 * lengthFieldOffset   =  1
 * lengthFieldLength   =  2
 * <b>lengthAdjustment</b>    = <b>-3</b> (= the length of HDR1 + LEN, negative)
 * <b>initialBytesToStrip</b> = <b> 3</b>
 *
 * BEFORE DECODE (16 bytes)                       AFTER DECODE (13 bytes)
 * +------+--------+------+----------------+      +------+----------------+
 * | HDR1 | Length | HDR2 | Actual Content |----->| HDR2 | Actual Content |
 * | 0xCA | 0x0010 | 0xFE | "HELLO, WORLD" |      | 0xFE | "HELLO, WORLD" |
 * +------+--------+------+----------------+      +------+----------------+
 * </pre>
 * @see LengthFieldPrepender
 */
public class LengthFieldBasedFrameDecoder extends ByteToMessageDecoder {
	// ....
}
```





## 二次解码器

```
- 为什么需要二次解码器？
- 一次解码器的结果是什么？
```



- 一次解码器的结果是字节数组。`ByteToMessageDecoder`
  - `ByteBuf` （原始数据）-> `ByteBuf` (用户数据)
- 二次解码器。`MessageToMessageDecoder`
  - `Bytebuf `-> `java Object`
- 

- 两个抽象：`ByteToMessageDecoder`, `MessageToMessageDecoder`

- `netty` 提供的所有编解码器都是这两个的子类




```
	+---------+								+---------+
    | ByteBuf |  ByteToMessageDecoder		| javaPOJO|
	+---------+								+---------+
	
	+---------+								+---------+
    | javaPOJO|  MessageToMessageDecoder	| javaPOJO|
	+---------+								+---------+
```





## 3.2. 解码器

```
- 解码器和 ChannelHandler 的关系是什么？
- 需要知道两个解码器：ReplayingDecoder， LengthFiledBasedFrameDecoder（更常用）
- LengthFiledBasedFrameDecoder 四大参数的意义是什么？
- 什么是分包传输？
```



- 解密器都是 `Inbound` 类型的 handler
- `ReplayingDecoder`
  - 特点：ByteBuf 够长度，则读；不够长度，则不读
  - 分包传输用到的 API
    - 状态相关
      -  设置状态：checkPoint(S)
      - 获取状态：state()

- 什么是分包传输？
  - [ ] **todo**













## 3.3. netty-codec



- netty 中最多的子模块就是 `codec-xxx`。比如：`codec-http`, `codec-http2`, `codec-xml`, `codec-redis`。说明：
     - 这些常用的协议，netty 帮你了，开发者不必关注这些协议了		
     - 协议解析工作也是使用 netty 时，一个重要的工作

- 为什么 codec 包下的接口少？

  - codec 下的都是 `encoder` 和 `decoder`，而 `encoder` 和 `decoder` 的顶层接口是：`ChannelHandler`，`ChannelInboundHandler`， `ChannelOutboundHandler`

  - `decoder`，`encoder` 基本上是 `ByteToMessageDecoder`, `MessageToMessageDecoder`这两个抽象类的实现





# 4. 优化你的 netty 应用



## 4.1. `LoggingHandler`



加上日志 `pipeline.addLast("infolog", new LoggingHandler(LogLevel.INFO))`还是很容易监测运行情况的。看下下面的对比

(另外，也可以加 `new LoggingHandler(LogLevel.DEBUG)` ) 



**没加**

```
13:30:00 [boss-1-1] AbstractInternalLogger: [id: 0xd3fedf01] REGISTERED
13:30:00 [boss-1-1] AbstractInternalLogger: [id: 0xd3fedf01] BIND: 0.0.0.0/0.0.0.0:8090
13:30:00 [boss-1-1] AbstractInternalLogger: [id: 0xd3fedf01, L:/0:0:0:0:0:0:0:0:8090] ACTIVE
13:30:04 [boss-1-1] AbstractInternalLogger: [id: 0xd3fedf01, L:/0:0:0:0:0:0:0:0:8090] READ: [id: 0xb3dc83a0, L:/127.0.0.1:8090 - R:/127.0.0.1:14150]
13:30:04 [boss-1-1] AbstractInternalLogger: [id: 0xd3fedf01, L:/0:0:0:0:0:0:0:0:8090] READ COMPLETE
```



**加上**

```
13:32:42 [boss-1-1] AbstractInternalLogger: [id: 0xae3e6fab] REGISTERED
13:32:42 [boss-1-1] AbstractInternalLogger: [id: 0xae3e6fab] BIND: 0.0.0.0/0.0.0.0:8090
13:32:42 [boss-1-1] AbstractInternalLogger: [id: 0xae3e6fab, L:/0:0:0:0:0:0:0:0:8090] ACTIVE
13:32:47 [boss-1-1] AbstractInternalLogger: [id: 0xae3e6fab, L:/0:0:0:0:0:0:0:0:8090] READ: [id: 0x49a64b61, L:/127.0.0.1:8090 - R:/127.0.0.1:14921]
13:32:47 [boss-1-1] AbstractInternalLogger: [id: 0xae3e6fab, L:/0:0:0:0:0:0:0:0:8090] READ COMPLETE
13:32:47 [worker-3-1] AbstractInternalLogger: [id: 0x49a64b61, L:/127.0.0.1:8090 - R:/127.0.0.1:14921] REGISTERED
13:32:47 [worker-3-1] AbstractInternalLogger: [id: 0x49a64b61, L:/127.0.0.1:8090 - R:/127.0.0.1:14921] ACTIVE
13:32:47 [worker-3-1] AbstractInternalLogger: [id: 0x49a64b61, L:/127.0.0.1:8090 - R:/127.0.0.1:14921] READ: Message(messageHeader=MessageHeader(version=1, opCode=3, streamId=1), messageBody=OrderOperation(tableId=1001, dish=tudou))
13:32:47 [worker-3-1] OrderOperation: order's executing startup with orderRequest: OrderOperation(tableId=1001, dish=tudou)
13:32:50 [worker-3-1] OrderOperation: order's executing complete
13:32:50 [worker-3-1] AbstractInternalLogger: [id: 0x49a64b61, L:/127.0.0.1:8090 - R:/127.0.0.1:14921] WRITE: Message(messageHeader=MessageHeader(version=1, opCode=3, streamId=1), messageBody=OrderOperationResult(tableId=1001, dish=tudou, complete=true))
13:32:50 [worker-3-1] AbstractInternalLogger: [id: 0x49a64b61, L:/127.0.0.1:8090 - R:/127.0.0.1:14921] FLUSH
13:32:50 [worker-3-1] AbstractInternalLogger: [id: 0x49a64b61, L:/127.0.0.1:8090 - R:/127.0.0.1:14921] READ COMPLETE
```





## 4.2. idle 检测

```
- netty 提供的 IdleStateHandler 参数如何使用？
- server 端：设置 10s 内，如果 channel 不可读，关闭该 channel
- client 端：设置 5s 内，该 channel 还没有出站消息，发送一条 keepalive 消息
```



**用例子解释`IdleStateHandler` 的状态参数**

```java
// An example that sends a ping message when there is no outbound traffic
// for 30 seconds.  The connection is closed when there is no inbound traffic
// for 60 seconds.

public class MyChannelInitializer extends ChannelInitializer<Channel> {
    @Override
    public void initChannel(Channel channel) {
        channel.pipeline().addLast("idleStateHandler", new IdleStateHandler(60, 30, 0));
        channel.pipeline().addLast("myHandler", new MyHandler());
    }
}

// Handler should handle the IdleStateEvent triggered by IdleStateHandler.
public class MyHandler extends ChannelDuplexHandler {
    @Override
    public void userEventTriggered(ChannelHandlerContext ctx, Object evt) throws Exception {
        if (evt instanceof IdleStateEvent) {
            IdleStateEvent e = (IdleStateEvent) evt;
            if (e.state() == IdleState.READER_IDLE) {
                ctx.close();
            } else if (e.state() == IdleState.WRITER_IDLE) {
                ctx.writeAndFlush(new PingMessage());
            }
        }
    }
}
```



**Server 端**

```java
@Slf4j
public class ServerIdleCheckHandler extends IdleStateHandler {
    // channel 已经 10s 不可读了
    public ServerIdleCheckHandler() {
        super(10, 0, 0, TimeUnit.SECONDS);
    }

    // channel idle 时会调用
    @Override
    protected void channelIdle(ChannelHandlerContext ctx, IdleStateEvent evt) throws Exception {
        if (evt == IdleStateEvent.FIRST_READER_IDLE_STATE_EVENT) {
            log.info("idle check happen, so close the connection");
            ctx.close();
            return;
        }

        super.channelIdle(ctx, evt);
    }
}
```



**Client 端**

![image-20220719170004131](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20220719170004131.png)



```java
// idleCheker
public class ClientIdleCheckHandler extends IdleStateHandler {

    public ClientIdleCheckHandler() {
        super(0, 5, 0);
    }

}


// keepalive handler
@Slf4j
@ChannelHandler.Sharable
public class KeepaliveHandler extends ChannelInboundHandlerAdapter {

    @Override
    public void userEventTriggered(ChannelHandlerContext ctx, Object evt) throws Exception {
        if (evt == IdleStateEvent.FIRST_WRITER_IDLE_STATE_EVENT) {
            log.info("write idle happen. so need to send keepalive to keep connection not closed by server");
            KeepaliveOperation keepaliveOperation = new KeepaliveOperation();
            RequestMessage requestMessage = new RequestMessage(IdUtil.nextId(), keepaliveOperation);
            ctx.writeAndFlush(requestMessage);
        }
        super.userEventTriggered(ctx, evt);
    }
}
```





## 4.3. 可视化

```
- 让应用报道当前连接数等
```



**需要的第三方包**

```
  <dependency>
            <groupId>io.dropwizard.metrics</groupId>
            <artifactId>metrics-core</artifactId>
            <version>4.1.1</version>
        </dependency>
```



**编写 reporter handler**

```java
@ChannelHandler.Sharable
public class MetricsHandler extends ChannelDuplexHandler {

    private AtomicLong totalConnectionNumber = new AtomicLong();

    {

        MetricRegistry metricRegistry = new MetricRegistry();

        metricRegistry.register("totalConnectionNumber", new Gauge<Long>() {
            @Override
            public Long getValue() {
                return totalConnectionNumber.longValue();
            }
        });

        // 输出到控制台
        ConsoleReporter consoleReporter = ConsoleReporter.forRegistry(metricRegistry).build();
        consoleReporter.start(10, TimeUnit.SECONDS);

        // 输出到 jmx
        JmxReporter jmxReporter = JmxReporter.forRegistry(metricRegistry).build();
        jmxReporter.start();

    }

    @Override
    public void channelActive(ChannelHandlerContext ctx) throws Exception {
        totalConnectionNumber.incrementAndGet();
        super.channelActive(ctx);
    }

    @Override
    public void channelInactive(ChannelHandlerContext ctx) throws Exception {
        totalConnectionNumber.decrementAndGet();
        super.channelInactive(ctx);
    }
}
```



之后，将该 handler 加入到  `child channel` 的 `pipeline` 中即可



**查看效果**

```
22-7-19 17:24:10 ===============================================================

-- Gauges ----------------------------------------------------------------------
totalConnectionNumber
             value = 1
```





## 4.4. 注解

```
- 常见注解 @Shareable, @Skip, @UnstableApi
```



- `@Sharable` 某些全局的 handler （全局统计信息等）应该加上该注解
- `@Skip` netty 内部自己使用，在一些版本中，使用者不能使用该注解
- `@UnstableApi` 仅仅标识作用，netty 内外都可用





**使用**

```java
ServerBootstrap serverBootstrap = new ServerBootstrap();
        
        serverBootstrap.channel(NioServerSocketChannel.class);
        // 1. 设置两个参数。调大 SO_BACKLOG；关闭 Nagle 算法
        serverBootstrap.option(NioChannelOption.SO_BACKLOG, 1024);
        serverBootstrap.childOption(NioChannelOption.TCP_NODELAY, true);
        serverBootstrap.handler(new LoggingHandler(LogLevel.INFO));

        //2. 给这两个线程池设置名称
        NioEventLoopGroup bossGroup = new NioEventLoopGroup(0, new DefaultThreadFactory("boss"));
        NioEventLoopGroup workGroup = new NioEventLoopGroup(0, new DefaultThreadFactory("worker"));
        try{

            serverBootstrap.group(bossGroup, workGroup);

            // 可以设置成全局的 handler
            MetricsHandler metricsHandler = new MetricsHandler();
            LoggingHandler debugLogHandler = new LoggingHandler(LogLevel.DEBUG);
            LoggingHandler infoLogHandler = new LoggingHandler(LogLevel.INFO);

            serverBootstrap.childHandler(new ChannelInitializer<NioSocketChannel>() {
                @Override
                protected void initChannel(NioSocketChannel ch) throws Exception {
                    ChannelPipeline pipeline = ch.pipeline();
                    
                    pipeline.addLast("metricHandler", metricsHandler);
                    pipeline.addLast("infolog", infoLogHandler);

            ChannelFuture channelFuture = serverBootstrap.bind(8090).sync();

            channelFuture.channel().closeFuture().sync();

        } finally{
            bossGroup.shutdownGracefully();
            workGroup.shutdownGracefully();
            businessGroup.shutdownGracefully();
            eventLoopGroupForTrafficShaping.shutdownGracefully();
        }

    }
```





## 4.5. `flush` 优化

```
- netty flush 优化：FlushConsolidationHandler
- Cassandra 的 flush 优化
```

> https://blog.csdn.net/xxcupid/article/details/105864151





- [ ] TODO





## 4.6. 防止内存泄漏

> 这部分内容很重要 TODO





- [ ] TODO





## 4.7. 其它



```
- 增加安全性方式1：让你的 netty 支持 SSL（这一部分不重要）
- 增加安全性方式2：配置 ip 白名单（相关类：IpSubnetFilterRule, RuleBasedIpFilter) (TODO)
```







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

- 

- 





# 6. 学习其它框架如何使用 Netty



## 6.1. xxl-job



1. `xxl-job` 写了个在执行器端启动了一个 `EmbedServer`。`EmbedServer` 两个作用：1. 主动发送 `Http` 请求到 `xxl-admin`调度器，2. 接受`xxl-admin` 调度器的 `Http` 请求

2. 用到了三个 `netty` 内置的 `ChannelHandler`。并且设置 `child channel` 为 `ChannelOption.SO_KEEPALIVE = true`

   - `IdleStateHandler`

   - `HttpServerCodec`

   - `HttpObjectAggregator`




```java
public class EmbedServer {
    private static final Logger logger = LoggerFactory.getLogger(EmbedServer.class);

    private ExecutorBiz executorBiz;
    private Thread thread;

    public void start(final String address, final int port, final String appname, final String accessToken) {
        executorBiz = new ExecutorBizImpl();
        thread = new Thread(new Runnable() {

            @Override
            public void run() {

                // param
                EventLoopGroup bossGroup = new NioEventLoopGroup();
                EventLoopGroup workerGroup = new NioEventLoopGroup();
                ThreadPoolExecutor bizThreadPool = new ThreadPoolExecutor(
                        0,
                        200,
                        60L,
                        TimeUnit.SECONDS,
                        new LinkedBlockingQueue<Runnable>(2000),
                        new ThreadFactory() {
                            @Override
                            public Thread newThread(Runnable r) {
                                return new Thread(r, "xxl-rpc, EmbedServer bizThreadPool-" + r.hashCode());
                            }
                        },
                        new RejectedExecutionHandler() {
                            @Override
                            public void rejectedExecution(Runnable r, ThreadPoolExecutor executor) {
                                throw new RuntimeException("xxl-job, EmbedServer bizThreadPool is EXHAUSTED!");
                            }
                        });


                try {
                    // start server
                    ServerBootstrap bootstrap = new ServerBootstrap();
                    bootstrap.group(bossGroup, workerGroup)
                            .channel(NioServerSocketChannel.class)
                            .childHandler(new ChannelInitializer<SocketChannel>() {
                                @Override
                                public void initChannel(SocketChannel channel) throws Exception {
                                    channel.pipeline()
                                            .addLast(new IdleStateHandler(0, 0, 30 * 3, TimeUnit.SECONDS))  // beat 3N, close if idle
                                            .addLast(new HttpServerCodec())
                                            .addLast(new HttpObjectAggregator(5 * 1024 * 1024))  // merge request & reponse to FULL
                                            .addLast(new EmbedHttpServerHandler(executorBiz, accessToken, bizThreadPool));
                                }
                            })
                            .childOption(ChannelOption.SO_KEEPALIVE, true);

                    // bind
                    ChannelFuture future = bootstrap.bind(port).sync();

                    logger.info(">>>>>>>>>>> xxl-job remoting server start success, nettype = {}, port = {}", EmbedServer.class, port);

                    // start registry
                    startRegistry(appname, address);

                    // wait util stop
                    future.channel().closeFuture().sync();

                } catch (InterruptedException e) {
                    if (e instanceof InterruptedException) {
                        logger.info(">>>>>>>>>>> xxl-job remoting server stop.");
                    } else {
                        logger.error(">>>>>>>>>>> xxl-job remoting server error.", e);
                    }
                } finally {
                    // stop
                    try {
                        workerGroup.shutdownGracefully();
                        bossGroup.shutdownGracefully();
                    } catch (Exception e) {
                        logger.error(e.getMessage(), e);
                    }
                }

            }

        });
        thread.setDaemon(true);	// daemon, service jvm, user thread leave >>> daemon leave >>> jvm leave
        thread.start();
    }

    public void stop() throws Exception {
        // destroy server thread
        if (thread!=null && thread.isAlive()) {
            thread.interrupt();
        }

        // stop registry
        stopRegistry();
        logger.info(">>>>>>>>>>> xxl-job remoting server destroy success.");
    }


    // ---------------------- registry ----------------------

    /**
     * netty_http
     *
     * Copy from : https://github.com/xuxueli/xxl-rpc
     *
     * @author xuxueli 2015-11-24 22:25:15
     */
    public static class EmbedHttpServerHandler extends SimpleChannelInboundHandler<FullHttpRequest> {
        private static final Logger logger = LoggerFactory.getLogger(EmbedHttpServerHandler.class);

        private ExecutorBiz executorBiz;
        private String accessToken;
        private ThreadPoolExecutor bizThreadPool;
        public EmbedHttpServerHandler(ExecutorBiz executorBiz, String accessToken, ThreadPoolExecutor bizThreadPool) {
            this.executorBiz = executorBiz;
            this.accessToken = accessToken;
            this.bizThreadPool = bizThreadPool;
        }

        @Override
        protected void channelRead0(final ChannelHandlerContext ctx, FullHttpRequest msg) throws Exception {

            // request parse
            //final byte[] requestBytes = ByteBufUtil.getBytes(msg.content());    // byteBuf.toString(io.netty.util.CharsetUtil.UTF_8);
            String requestData = msg.content().toString(CharsetUtil.UTF_8);
            String uri = msg.uri();
            HttpMethod httpMethod = msg.method();
            boolean keepAlive = HttpUtil.isKeepAlive(msg);
            String accessTokenReq = msg.headers().get(XxlJobRemotingUtil.XXL_JOB_ACCESS_TOKEN);

            // invoke
            bizThreadPool.execute(new Runnable() {
                @Override
                public void run() {
                    // do invoke
                    Object responseObj = process(httpMethod, uri, requestData, accessTokenReq);

                    // to json
                    String responseJson = GsonTool.toJson(responseObj);

                    // write response
                    writeResponse(ctx, keepAlive, responseJson);
                }
            });
        }

        private Object process(HttpMethod httpMethod, String uri, String requestData, String accessTokenReq) {

            // valid
            if (HttpMethod.POST != httpMethod) {
                return new ReturnT<String>(ReturnT.FAIL_CODE, "invalid request, HttpMethod not support.");
            }
            if (uri==null || uri.trim().length()==0) {
                return new ReturnT<String>(ReturnT.FAIL_CODE, "invalid request, uri-mapping empty.");
            }
            if (accessToken!=null
                    && accessToken.trim().length()>0
                    && !accessToken.equals(accessTokenReq)) {
                return new ReturnT<String>(ReturnT.FAIL_CODE, "The access token is wrong.");
            }

            // services mapping
            try {
                if ("/beat".equals(uri)) {
                    return executorBiz.beat();
                } else if ("/idleBeat".equals(uri)) {
                    IdleBeatParam idleBeatParam = GsonTool.fromJson(requestData, IdleBeatParam.class);
                    return executorBiz.idleBeat(idleBeatParam);
                } else if ("/run".equals(uri)) {
                    TriggerParam triggerParam = GsonTool.fromJson(requestData, TriggerParam.class);
                    return executorBiz.run(triggerParam);
                } else if ("/kill".equals(uri)) {
                    KillParam killParam = GsonTool.fromJson(requestData, KillParam.class);
                    return executorBiz.kill(killParam);
                } else if ("/log".equals(uri)) {
                    LogParam logParam = GsonTool.fromJson(requestData, LogParam.class);
                    return executorBiz.log(logParam);
                } else {
                    return new ReturnT<String>(ReturnT.FAIL_CODE, "invalid request, uri-mapping("+ uri +") not found.");
                }
            } catch (Exception e) {
                logger.error(e.getMessage(), e);
                return new ReturnT<String>(ReturnT.FAIL_CODE, "request error:" + ThrowableUtil.toString(e));
            }
        }

        /**
         * write response
         */
        private void writeResponse(ChannelHandlerContext ctx, boolean keepAlive, String responseJson) {
            // write response
            FullHttpResponse response = new DefaultFullHttpResponse(HttpVersion.HTTP_1_1, HttpResponseStatus.OK, Unpooled.copiedBuffer(responseJson, CharsetUtil.UTF_8));   //  Unpooled.wrappedBuffer(responseJson)
            response.headers().set(HttpHeaderNames.CONTENT_TYPE, "text/html;charset=UTF-8");       // HttpHeaderValues.TEXT_PLAIN.toString()
            response.headers().set(HttpHeaderNames.CONTENT_LENGTH, response.content().readableBytes());
            if (keepAlive) {
                response.headers().set(HttpHeaderNames.CONNECTION, HttpHeaderValues.KEEP_ALIVE);
            }
            ctx.writeAndFlush(response);
        }

        @Override
        public void channelReadComplete(ChannelHandlerContext ctx) throws Exception {
            ctx.flush();
        }

        @Override
        public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) {
            logger.error(">>>>>>>>>>> xxl-job provider netty_http server caught exception", cause);
            ctx.close();
        }

        @Override
        public void userEventTriggered(ChannelHandlerContext ctx, Object evt) throws Exception {
            if (evt instanceof IdleStateEvent) {
                ctx.channel().close();      // beat 3N, close if idle
                logger.debug(">>>>>>>>>>> xxl-job provider netty_http server close an idle channel.");
            } else {
                super.userEventTriggered(ctx, evt);
            }
        }
    }

    // ---------------------- registry ----------------------

    //
    public void startRegistry(final String appname, final String address) {
        // start registry
        ExecutorRegistryThread.getInstance().start(appname, address);
    }

    public void stopRegistry() {
        // stop registry
        ExecutorRegistryThread.getInstance().toStop();
    }


}
```



### 6.1.1. `HttpServerCodec`



<u>*具体怎么编解码的没看，看不懂（呜呜┭┮﹏┭┮）*</u>







### 6.1.2. `HttpObjectAggregator`



感觉在 `netty` 中读了每个 class 的文档描述后，就会使用了，`netty` 的文档写的真是详细



看下 `HttpObjectAggregator` 的文档描述

```
A ChannelHandler that aggregates an HttpMessage and its following HttpContents into a single FullHttpRequest or FullHttpResponse (depending on if it used to handle requests or responses) with no following HttpContents. It is useful when you don't want to take care of HTTP messages whose transfer encoding is 'chunked'. Insert this handler after HttpResponseDecoder in the ChannelPipeline if being used to handle responses, or after HttpRequestDecoder and HttpResponseEncoder in the ChannelPipeline if being used to handle requests.
    ChannelPipeline p = ...;
    ...
    p.addLast("decoder", new HttpRequestDecoder());
    p.addLast("encoder", new HttpResponseEncoder());
    p.addLast("aggregator", new HttpObjectAggregator(1048576));
    ...
    p.addLast("handler", new HttpRequestHandler());
    
For convenience, consider putting a HttpServerCodec before the HttpObjectAggregator as it functions as both a HttpRequestDecoder and a HttpResponseEncoder.
Be aware that HttpObjectAggregator may end up sending a HttpResponse: 
Response Status
Condition When Sent
100 Continue
A '100-continue' expectation is received and the 'content-length' doesn't exceed maxContentLength
417 Expectation Failed
A '100-continue' expectation is received and the 'content-length' exceeds maxContentLength
413 Request Entity Too Large
Either the 'content-length' or the bytes received so far exceed maxContentLength
See Also:
FullHttpRequest, FullHttpResponse, HttpResponseDecoder, HttpServerCodec
```



结论：

- `HttpObjectAggregator` 将 `HttpMessage` 和 `HttpContent` 合在一起了（并且不需要关系 http 通信是否是分块传输的 `transfer-encoding: chunked`)
- pipleline 中最佳添加方式：`HttpServerCodec` -> `HttpObjectAggregator`





# 7. 参考文档

>1. [netty 4.0 新特性](http://ifeve.com/netty-4-0-new/#more-32469) 
>1. [分块传输编码](https://zh.wikipedia.org/wiki/分块传输编码#:~:text=分块传输编码（Chunked,HTTP%2F1.1）中提供。)

