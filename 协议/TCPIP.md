# 协议栈

- 应用层
- 传输层
- 网络层
- 数据链路层
- 物理层



# TCP



## 三次握手

```
- 为什么需要三次握手，而不是两次或四次呢？
- 三次握手发送的包分别是什么？握手过程发送方和接受方，状态是怎么改变的？
```



**包格式+状态改变**



<img src="https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220901114241.png" alt="image-20220901114241746" style="zoom:25%;" />



**序列号**

<img src="https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220901113623.png" alt="image-20220901113615962" style="zoom: 33%;" />



- step 1: 发送方发送一个 SYN 包，里面包含一个序列号。与 server 通信的包以该序列号开始



## 连接队列和半连接队列

```
- 什么情况下进入半连接队列
- 什么情况下进入全连接队列
- 什么情况下从全连接队列出来
- 全连接队列大小限制？
- 全连接队列满了之后会怎么样？
```



<img src="https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220901112811.png" alt="image-20220901112811451" style="zoom: 25%;" />

- 连接大小限制：

  - 半连接大小：max（64，/proc/sys/net/ipv4/tcp_max_syn_backlog)
  - 全连接队列大小：min(backlog, somaxconn)
    - backlog: `listen(int sockfd, int backlog)` 函数传入的值
    - somaxconn: 由内核参数 `/proc/sys/net/core/somaxconn` 决定

- 查看系统的全连接队列大小：

  ```
  [root@a]# ss -lnt 'sport = :8080'
  State      Recv-Q Send-Q         Local Address:Port       Peer Address:Port       
  LISTEN     0      100                *:8080   					*:*        
  ```

  - `Send-Q`: 全连接队列大小
  - `Recv-Q`: （这个不知道和 `send-q` 什么关系——



- 全连接队列满了之后会怎么样？

  - 取决于内核参数：`cat /proc/sys/ipv4/tcp_absort_on_overflow`

  <img src="https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220901115801.png" style="zoom: 33%;" />

  

  **这里有些疑问：**

  1. 全连接队列和 backlog 队列的关系是什么？
  2. `ss -lnt 'sport = :8080'` 中的 `Send-Q` 是 baklog 队列吗？



## 四次挥手

<img src="https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220901121006.png" alt="image-20220901121006460" style="zoom: 25%;" />



客户端发送完最后一个 `ACK` 时，并不是直接转为 `close` 状态，而是进入 `TIME-WAIT` 状态，而是需要等待 `2MSL` 个时间后，才会释放连接。**这是一个少有的应用退出，资源还没释放的例子**



**`TIME_WAIT` 到 `close` 为什么需要 2MSL 时间呢？**

-  b 给 a 发了 `FIN+ACK` 后进入 `LAST-ACK` 状态，之后接收到 A 的 `ACK` 后，才会进入 `CLOSE` 状态。如果 B 在 2MSL 时间段内 没有收到 A 的 `ACK` ，B 就会重发 `FIN+ACK`。对 A 来说，如果 2 MSL 时间段内没有收到 `FIN+ACK` 就会进入 `CLOSE` 状态

**MSL 大小是多少，可以配置吗？**

- 查看大小：`cat /proc/sys/net/ipv4/tcp_fin_timeout`
- 修改大小：`echo 30 > /proc/sys/net/ipv4/tcp_fin_timeout`



## TCP 包格式



### tcp header

![image-20220901222011390](https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220901222011.png)







# ip



## ip 包格式

<img src="https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220901224222.png" alt="image-20220901224222517" style="zoom:33%;" />



- 首部
  - 版本：4：ipv4, 6: ipv6
  - 首部长度。比如是 2，则首部大小 = 2 * 4 byte = 4 字节
  - 服务类型（不重要）
  - 总长度。比如是 5 。则总长度：5 * 2byte = 10 字节
  - 标识。因为一个完整的 ip 报文在发送的时候会被分片发出。对端接受后，根据该值重新组成一个完整的包
  - 标志：
  - 片偏移。该 ip 分包在原始 ip 包中的位置
  - 生存时间。以跳数为单位。每被路由器转发一次，就会减一。ttl 为 0 时，会被丢弃。防止随意转发
  - 协议。传输层协议：tcp, udp, icmp
  - 源地址。源 ip 地址
  - 目的地址。目的 ip 地址
  - 可选字段。~~这里先不写~~
  - 填充字段。保证首部 4 字节对齐

 