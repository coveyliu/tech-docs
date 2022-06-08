# NIO 三剑客



## Buffer

- `Buffer` 视作一个容器
- `Buffer` 是一个抽象类，具体实现有 `ByteBuffer`, `ShortBuffer`, `IntBuffer`, `LongBuffer`, `FloatBuffer`, `DoubleBuffer`, `CharBuffer`。最常用的是 `ByteBuffer`
- 重要属性: `int position`, `int limit`, `int capacity`
- 重要 API:
  - `rewind()`。重读，从 position 出重读
  - `mark() | reset()`。`mark()` 记录此时的 `position`, `reset()` 将 position 设置为 mark 处
- 需要了解的特性
  - `Buffer` 有两种模式，读模式和写模式。`Buffer` 被新建的时候处于写模式。调用 `flip()` 可以从写模式切换到读模式
  - 往 `Buffer` 中写数据的时候 `position` 会移动。从 `Buffer` 读数据的时候 `position` 也会移动





## Channel



<img width=70% src='https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220608221759.png'>





看下 `Channel` 的 doc 文档。个人感觉对理解 `Channel` 是有帮助的

```java

/**
 * A nexus for I/O operations.
 *
 * <p> A channel represents an open connection to an entity such as a hardware
 * device, a file, a network socket, or a program component that is capable of
 * performing one or more distinct I/O operations, for example reading or
 * writing.
 *
 * <p> A channel is either open or closed.  A channel is open upon creation,
 * and once closed it remains closed.  Once a channel is closed, any attempt to
 * invoke an I/O operation upon it will cause a {@link ClosedChannelException}
 * to be thrown.  Whether or not a channel is open may be tested by invoking
 * its {@link #isOpen isOpen} method.
 *
 * <p> Channels are, in general, intended to be safe for multithreaded access
 * as described in the specifications of the interfaces and classes that extend
 * and implement this interface.
 *
 *
 * @author Mark Reinhold
 * @author JSR-51 Expert Group
 * @since 1.4
 */

public interface Channel extends Closeable {...}
```

总结一下：

1. <font color=#ff000>一个 `Channel` 代表一个连接。这个连接可以是连接到文件（`file`, 网络套接字）</font>
2. <font color=red>并且这个连接支持 `I/O` 操作</font>



看下 `NetworkChannel` 的 doc 文档

```java

/**
 * A channel to a network socket.
 *
 * <p> A channel that implements this interface is a channel to a network
 * socket. The {@link #bind(SocketAddress) bind} method is used to bind the
 * socket to a local {@link SocketAddress address}, the {@link #getLocalAddress()
 * getLocalAddress} method returns the address that the socket is bound to, and
 * the {@link #setOption(SocketOption,Object) setOption} and {@link
 * #getOption(SocketOption) getOption} methods are used to set and query socket
 * options.  An implementation of this interface should specify the socket options
 * that it supports.
 *
 * <p> The {@link #bind bind} and {@link #setOption setOption} methods that do
 * not otherwise have a value to return are specified to return the network
 * channel upon which they are invoked. This allows method invocations to be
 * chained. Implementations of this interface should specialize the return type
 * so that method invocations on the implementation class can be chained.
 *
 * @since 1.7
 */

public interface NetworkChannel
    extends Channel{
    NetworkChannel bind(SocketAddress local) throws IOException;
        <T> NetworkChannel setOption(SocketOption<T> name, T value) throws IOException;

}
```



总结：

1. `NetworkChannel` 是连接到网络套接字的连接
2. `bind()` 方法的目的是将一个 `socket` 绑定到某个地址上
3. `bind()`, `setOption()` 方法的返回值是 `NetworkChannel` ，这支持链式编程

4. 连接到 `file` 的 channel，你应该可以想到是：`FileChannel` ，答案是肯定的





### `SocketChannel` 和 `SeverSocketChannel`



这两个类都是抽象类。调用它们提供的静态方法可以生成一个实现类

- `ServerSocketChannel.open()`
- `SocketChannel.open()`





## Selector



- `Selector` 是一个抽象类。使用静态方法 `Selector.open()` 可以获取一个 `Selector`
- `select()`, `select(long)` 是阻塞的。下面的操作会让这两个方法立即返回，不再阻塞
  1. 调用 selector 的 `wakeup` 方法







### `SelectionKey`



