<!-- TOC -->

- [1. 进程](#1-%E8%BF%9B%E7%A8%8B)
    - [1.1. 进程基础](#11-%E8%BF%9B%E7%A8%8B%E5%9F%BA%E7%A1%80)
        - [1.1.1. 服务](#111-%E6%9C%8D%E5%8A%A1)
        - [1.1.2. 特殊的进程管理](#112-%E7%89%B9%E6%AE%8A%E7%9A%84%E8%BF%9B%E7%A8%8B%E7%AE%A1%E7%90%86)
    - [1.2. bash 下的任务管理](#12-bash-%E4%B8%8B%E7%9A%84%E4%BB%BB%E5%8A%A1%E7%AE%A1%E7%90%86)
    - [1.3. 工作管理](#13-%E5%B7%A5%E4%BD%9C%E7%AE%A1%E7%90%86)
        - [1.3.1. 什么是工作管理](#131-%E4%BB%80%E4%B9%88%E6%98%AF%E5%B7%A5%E4%BD%9C%E7%AE%A1%E7%90%86)
        - [1.3.2. job control 管理](#132-job-control-%E7%AE%A1%E7%90%86)
        - [1.3.3. 脱机管理](#133-%E8%84%B1%E6%9C%BA%E7%AE%A1%E7%90%86)
    - [1.4. 进程管理](#14-%E8%BF%9B%E7%A8%8B%E7%AE%A1%E7%90%86)
        - [1.4.1. 观察进程](#141-%E8%A7%82%E5%AF%9F%E8%BF%9B%E7%A8%8B)
        - [1.4.2. 调整优先级](#142-%E8%B0%83%E6%95%B4%E4%BC%98%E5%85%88%E7%BA%A7)
        - [1.4.3. 给进程发信号](#143-%E7%BB%99%E8%BF%9B%E7%A8%8B%E5%8F%91%E4%BF%A1%E5%8F%B7)
        - [1.4.4. 守护进程（<u>TODO</u>](#144-%E5%AE%88%E6%8A%A4%E8%BF%9B%E7%A8%8Butodou)
        - [1.4.5. nohub](#145-nohub)
        - [1.4.6. 系统日志](#146-%E7%B3%BB%E7%BB%9F%E6%97%A5%E5%BF%97)
        - [1.4.7. systemctl <u>TODO</u>](#147-systemctl-utodou)
        - [1.4.8. 观察系统资源](#148-%E8%A7%82%E5%AF%9F%E7%B3%BB%E7%BB%9F%E8%B5%84%E6%BA%90)
            - [1.4.8.1. free 查看物理内存](#1481-free-%E6%9F%A5%E7%9C%8B%E7%89%A9%E7%90%86%E5%86%85%E5%AD%98)
            - [1.4.8.2. 查看系统核心相关信息](#1482-%E6%9F%A5%E7%9C%8B%E7%B3%BB%E7%BB%9F%E6%A0%B8%E5%BF%83%E7%9B%B8%E5%85%B3%E4%BF%A1%E6%81%AF)
            - [1.4.8.3. 观察系统开机时间和负载](#1483-%E8%A7%82%E5%AF%9F%E7%B3%BB%E7%BB%9F%E5%BC%80%E6%9C%BA%E6%97%B6%E9%97%B4%E5%92%8C%E8%B4%9F%E8%BD%BD)
            - [1.4.8.4. 查看网络情况](#1484-%E6%9F%A5%E7%9C%8B%E7%BD%91%E7%BB%9C%E6%83%85%E5%86%B5)
            - [1.4.8.5. 查看核心侦测的信息](#1485-%E6%9F%A5%E7%9C%8B%E6%A0%B8%E5%BF%83%E4%BE%A6%E6%B5%8B%E7%9A%84%E4%BF%A1%E6%81%AF)
- [2. SELinux](#2-selinux)

<!-- /TOC -->

# 1. 进程



## 1.1. 进程基础



- 在 linux 中，每个进程都有一个 PID
- 用户登录 linux 时，在 shell 上，每执行一个指令，都会有一个 PID 对应该指令，但是这种方式触发的 PID 很快就终止了
- 父进程和子进程
  - 子进程是由父进程衍生出来的
  - 子进程有一个 PPID 



```bash
[root@iZ8vbcd5pkcyga25fmapvtZ ~]# ps -l
F S   UID   PID  PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD
4 S     0 16181 16177  0  80   0 - 28893 do_wai pts/0    00:00:00 bash
0 R     0 16231 16181  0  80   0 - 38309 -      pts/0    00:00:00 ps
[root@iZ8vbcd5pkcyga25fmapvtZ ~]# bash
[root@iZ8vbcd5pkcyga25fmapvtZ ~]# ps -l
F S   UID   PID  PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD
4 S     0 16181 16177  0  80   0 - 28893 do_wai pts/0    00:00:00 bash
4 S     0 16275 16181  0  80   0 - 28893 do_wai pts/0    00:00:00 bash
0 R     0 16310 16275  0  80   0 - 38309 -      pts/0    00:00:00 ps
[root@iZ8vbcd5pkcyga25fmapvtZ ~]# exit
exit
[root@iZ8vbcd5pkcyga25fmapvtZ ~]# ps -l
F S   UID   PID  PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD
4 S     0 16181 16177  0  80   0 - 28893 do_wai pts/0    00:00:00 bash
0 R     0 16464 16181  0  80   0 - 38309 -      pts/0    00:00:00 ps
```



### 1.1.1. 服务

常驻内存的进程，在 linux 中称为服务，

`daemon` 类型的程序，都是以 `d` 结尾 ,形如：`xxxd`, `d` 是 `daemon` 的意思

比如：`systemd`, `httpd`, `crond`, `atd`





### 1.1.2. 特殊的进程管理



Linux 会宕机吗？



## 1.2. bash 下的任务管理



```bash
[root@iZwz90l6z42ze1mdxvb9giZ a]# cp a.txt ba.txt & ls
[1] 7564
a.txt  b  b-so  c
[root@iZwz90l6z42ze1mdxvb9giZ a]# cp notExists.txt test.text & ls
[2] 7701
a.txt  b  ba.txt  b-so  c
[1]   Done                    cp -i a.txt ba.txt
[root@iZwz90l6z42ze1mdxvb9giZ a]# cp: cannot stat ‘notExists.txt’: No such file or directory
```

`$` ，放在背景（backgroud）后执行该指令。（好像即使前一个指令出错了，也不影响后面指令的执行）



## 1.3. 工作管理



### 1.3.1. 什么是工作管理



我们进入系统，获得一个 bash shell 后，能不能一边复制文件，一边编译，一边用 vim 编写文本呢？答案是可以的。这就是 job controll，在一个终端机接口下同时进行多个工作的行为管理



### 1.3.2. job control 管理



- `$` 将工作丢到 bash 背景下执行
- `fg` 将 bash 背景下的工作放到前景中执行
- `bg` 将背景下的 stopped 的工作恢复运行



- `&` 可以将某一个工作丢到背景中执行
- 当前正在执行工作 `ctrl+z` 暂停，并放到背景中
- 



```bash
[root@iZwz90l6z42ze1mdxvb9giZ a]# cp a.txt a-back.text &
[1] 8300
[root@iZwz90l6z42ze1mdxvb9giZ a]# vim a-back.text 
[1]   Done                    cp -i a.txt a-back.text

[2]+  Stopped                 vim a-back.text
[root@iZwz90l6z42ze1mdxvb9giZ a]# 
```



### 1.3.3. 脱机管理



- nohup 配合 `$` ,可以在终端机结束的时候，依然已背景方式执行



## 1.4. 进程管理



### 1.4.1. 观察进程

观察进程的命令有：

1. `ps`
2. `pstree`
3. `top`

下面是我对上述三个命令的总结：

1. `ps` 就记住一个命令就行了 `ps -aux` ，可以查看所有的进程
2. `pstree` 用的少
3. `top` 需要记住下面几点：
   - 执行 `top` 时，按 `1` 可以查看所有 CPU
   - 执行 `top` 时，按 `s` 可以重置面板刷新时间（默认3s)
   - 可以仅查看一个进程 `top -p pid`



<img width="80%" src="https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220405173337.png">









### 1.4.2. 调整优先级

- 使用的指令
  - nice
  - renice，（进程运行期间，就可以调整优先级）
  - 使用方式：`nice|renice pid 新值`
- nice 值范围
  - -20 - 19，值越小优先级越高，抢占资源就越多
- 一个进程启动时
  - 默认的 nice 值 0
  - 进程父进程的 UID，所以，可执行程序跟用户息息相关





### 1.4.3. 给进程发信号



信号是进程间通信方式之一，典型用法：用户执行 `kill -9 pid` ，将某个进程终止；前台进程运行时，执行 ctrl+c，就是发送的 sigint 信息（相当于 `kill -2 pid`）



- 使用 `kill -l` ，可以查看 linux 支持哪些信号及信号对应值
- 如果想让你的脚本不被 `ctrl+c` 中断（相当于 `kill -2 pid`)，可以专门处理该信号
- `kill -9 ` 信号不能被阻塞和在脚本中处理



```bash
[root@iZwz90l6z42ze1mdxvb9giZ ~]# kill -l
 1) SIGHUP	 2) SIGINT	 3) SIGQUIT	 4) SIGILL	 5) SIGTRAP
 6) SIGABRT	 7) SIGBUS	 8) SIGFPE	 9) SIGKILL	10) SIGUSR1
11) SIGSEGV	12) SIGUSR2	13) SIGPIPE	14) SIGALRM	15) SIGTERM
16) SIGSTKFLT	17) SIGCHLD	18) SIGCONT	19) SIGSTOP	20) SIGTSTP
21) SIGTTIN	22) SIGTTOU	23) SIGURG	24) SIGXCPU	25) SIGXFSZ
26) SIGVTALRM	27) SIGPROF	28) SIGWINCH	29) SIGIO	30) SIGPWR
31) SIGSYS	34) SIGRTMIN	35) SIGRTMIN+1	36) SIGRTMIN+2	37) SIGRTMIN+3
38) SIGRTMIN+4	39) SIGRTMIN+5	40) SIGRTMIN+6	41) SIGRTMIN+7	42) SIGRTMIN+8
43) SIGRTMIN+9	44) SIGRTMIN+10	45) SIGRTMIN+11	46) SIGRTMIN+12	47) SIGRTMIN+13
48) SIGRTMIN+14	49) SIGRTMIN+15	50) SIGRTMAX-14	51) SIGRTMAX-13	52) SIGRTMAX-12
53) SIGRTMAX-11	54) SIGRTMAX-10	55) SIGRTMAX-9	56) SIGRTMAX-8	57) SIGRTMAX-7
58) SIGRTMAX-6	59) SIGRTMAX-5	60) SIGRTMAX-4	61) SIGRTMAX-3	62) SIGRTMAX-2
63) SIGRTMAX-1	64) SIGRTMAX	
```





### 1.4.4. 守护进程（<u>TODO</u>)



> TODO







### 1.4.5. nohub



- nohub 和 & 可以让一个命令，忽略（hangup）信号。示例：
  - `nohub tail -f /var/log/messages &` 
  - `nohub java -jar xxx.jar &`
- nohub 和 & 运行的进程并不是 守护进程
- nohub & 用终端启动一个命令后，关闭终端，那么该进程的父进程就是 1
  - 原因：关闭终端，该进程是孤儿进程，被 1 进程收留了
- 和守护进程相同的地方
  - **进程不受终端是否关闭影响**







### 1.4.6. 系统日志

- 位置，`/var/logs`，需要关注下面的日志
  - `messages`, 常规日志
  - `dmesg`, 内核日志
  - `secure`, 安全日志
  - `cron`, 定时任务日志



### 1.4.7. systemctl (<u>TODO</u>)



> TODO







### 1.4.8. 观察系统资源



#### 1.4.8.1. free 查看物理内存

```bash
[root@iZwz90l6z42ze1mdxvb9giZ ~]# free
              total        used        free      shared  buff/cache   available
Mem:        1882576      338856      102764         552     1440956     1355692
Swap:             0           0           0
[root@iZwz90l6z42ze1mdxvb9giZ ~]# free -h
              total        used        free      shared  buff/cache   available
Mem:           1.8G        329M        101M        552K        1.4G        1.3G
Swap:            0B          0B          0B
[root@iZwz90l6z42ze1mdxvb9giZ ~]# 
```

`free` 不加参数，单位是 `kb`, 使用 `-h` 选项，让结果更加易读（因为，后面加了单位啦）

对 `free` 显示的列说明：

- `shared/buff/cache` : 被缓存文件占用的容量。linux 会将经常使用的或最近使用的文件 cache 到内存，方便用的时候快速获取。这部分内存，在适当的时候是可以被释放的
- `available`: 可以使用的物理内存（可以简单的认为是：free + cached)

另外，一定要注意 `swap` 行的显示。因为 `swap` 分区本身是磁盘的空间。当系统用这个分区的空间做内存使用时，表示，系统物理内存已经严重不足了。最好是 `swap` 的 `used` 值为 0



#### 1.4.8.2. 查看系统核心相关信息

```bash
[root@iZwz90l6z42ze1mdxvb9giZ ~]# uname -a
Linux iZwz90l6z42ze1mdxvb9giZ 3.10.0-862.14.4.el7.x86_64 #1 SMP Wed Sep 26 15:12:11 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux
[root@iZwz90l6z42ze1mdxvb9giZ ~]# 
```

上面的结果说明：

- 系统是`linux`
- 主机名：`iZwz90l6z42ze1mdxvb9giZ`
- 核心版本：3.10.0-862.14.4.el7.x86_64
- 适用的硬件平台：x86_64





#### 1.4.8.3. 观察系统开机时间和负载

`uptime` 显示开机时间和前 1，5，15 分钟负载

显示的就是 `top` 第一行的内容。建议还是用 `top` , `top` 可以动态显示

```bash
[root@iZwz90l6z42ze1mdxvb9giZ ~]# uptime
 15:25:06 up 7 days, 22:00,  2 users,  load average: 0.04, 0.04, 0.05
```



#### 1.4.8.4. 查看网络情况

`netstat`  是 `net-tools` package 提供的一个功能，你可以 `yum info net-tools` 查看这个包的说明。有的机器可能没有安装 `net-tools`, 可以用 `yum install net-tools` 安装

`netstat` 可以

1. 查看当前有多少个人登录
2. 可以查看 socket 文件
3. 可以查看网络情况（哪些进程监听了哪些端口，流量多大等



<img width=50% src='https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220521160133.png'>



```bash
`216.54.35.58` 正是我的 ip，而且，我用 xshell 同时打开了两个标签连接
[root@iZwz90l6z42ze1mdxvb9giZ ~]# netstat
Active Internet connections (w/o servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp        0      0 iZwz90l6z42ze1mdxvb:x11 167.99.155.216:51656    SYN_RECV   
tcp        0      0 iZwz90l6z42ze1mdx:59912 100.100.2.148:http      TIME_WAIT  
tcp        0      0 iZwz90l6z42ze1mdx:37272 server-18-155-68-:https TIME_WAIT  
tcp        0      0 iZwz90l6z42ze1mdx:59906 100.100.2.148:http      TIME_WAIT  
tcp        0      0 iZwz90l6z42ze1mdx:43770 151.101.110.217:http    TIME_WAIT  
tcp        0      0 iZwz90l6z42ze1mdx:51050 100.100.30.26:http      ESTABLISHED
tcp        0      0 iZwz90l6z42ze1mdx:42058 100.100.0.70:https      TIME_WAIT  
tcp        0      0 iZwz90l6z42ze1mdx:59916 100.100.2.148:http      TIME_WAIT  
tcp        0      0 iZwz90l6z42ze1mdxvb:ssh 216.54.35.58.broad:5871 ESTABLISHED
tcp        0      0 iZwz90l6z42ze1mdx:57488 100.100.0.70:https      ESTABLISHED
tcp        0      0 iZwz90l6z42ze1mdx:59914 100.100.2.148:http      TIME_WAIT  
tcp        0     36 iZwz90l6z42ze1mdxvb:ssh 216.54.35.58.broad:6165 ESTABLISHED
tcp6       0      0 iZwz90l6z42ze1mdxvb:x11 167.99.155.216:51656    ESTABLISHED
tcp6       0      0 iZwz90l6z42ze1mdxvb:x11 167.99.155.216:60316    TIME_WAIT  
tcp6       0      0 iZwz90l6z42ze1mdxvb:x11 167.99.155.216:38738    TIME_WAIT  
tcp6       0      0 iZwz90l6z42ze1mdxvb:x11 167.99.155.216:43278    ESTABLISHED
tcp6       0      0 iZwz90l6z42ze1mdxvb:x11 167.99.155.216:47990    ESTABLISHED
tcp6       0      0 iZwz90l6z42ze1mdxvb:x11 167.99.155.216:46728    TIME_WAIT  
tcp6       0      0 iZwz90l:afs3-fileserver 117.158.148.162:36004   ESTABLISHED
tcp6       0      0 iZwz90l6z42ze1mdxvb:x11 167.99.155.216:60062    TIME_WAIT  
tcp6       0      0 iZwz90l6z42ze1mdxvb:x11 167.99.155.216:40052    TIME_WAIT  
tcp6       0      0 iZwz90l6z42ze1mdxvb:x11 167.99.155.216:45174    ESTABLISHED
tcp6       0      0 iZwz90l6z42ze1mdxvb:x11 167.99.155.216:50824    TIME_WAIT  
tcp6       0      0 iZwz90l6z42ze1mdxvb:x11 193.106.191.45:58998    TIME_WAIT  
Active UNIX domain sockets (w/o servers)
Proto RefCnt Flags       Type       State         I-Node   Path
unix  10     [ ]         DGRAM                    7167     /dev/log
unix  2      [ ]         DGRAM                    17215    /var/run/chrony/chronyd.sock
unix  3      [ ]         DGRAM                    7142     /run/systemd/notify
unix  2      [ ]         DGRAM                    7144     /run/systemd/cgroups-agent
unix  2      [ ]         DGRAM                    11248    /run/systemd/shutdownd
unix  2      [ ]         DGRAM                    22195    @00016
unix  6      [ ]         DGRAM                    7165     /run/systemd/journal/socket
unix  3      [ ]         STREAM     CONNECTED     16983    
unix  3      [ ]         STREAM     CONNECTED     17025    
unix  3      [ ]         STREAM     CONNECTED     17254    /run/dbus/system_bus_socket
unix  3      [ ]         STREAM     CONNECTED     17207    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     17329    /run/dbus/system_bus_socket
unix  3      [ ]         STREAM     CONNECTED     22923    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     17222    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     22921    
unix  3      [ ]         STREAM     CONNECTED     17253    
unix  3      [ ]         STREAM     CONNECTED     17205    /run/systemd/journal/stdout
unix  2      [ ]         DGRAM                    18886    
unix  3      [ ]         STREAM     CONNECTED     21010    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     17104    
unix  3      [ ]         STREAM     CONNECTED     44480    /usr/local/aegis/Aegis-<Guid(5A2C30A2-A87D-490A-9281-6765EDAD7CBA)>
unix  3      [ ]         STREAM     CONNECTED     17307    
unix  3      [ ]         STREAM     CONNECTED     17064    
unix  2      [ ]         DGRAM                    4884972  
unix  3      [ ]         STREAM     CONNECTED     22228    
unix  3      [ ]         STREAM     CONNECTED     22229    /run/containerd/containerd.sock
unix  2      [ ]         DGRAM                    22202    
unix  3      [ ]         STREAM     CONNECTED     20921    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     20051    
unix  3      [ ]         STREAM     CONNECTED     20910    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     20849    
unix  3      [ ]         STREAM     CONNECTED     20908    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     4193648  
unix  2      [ ]         DGRAM                    20928    
unix  3      [ ]         STREAM     CONNECTED     17255    /run/dbus/system_bus_socket
unix  3      [ ]         STREAM     CONNECTED     22223    /run/containerd/containerd.sock
unix  2      [ ]         DGRAM                    18633    
unix  2      [ ]         DGRAM                    21524    
unix  3      [ ]         STREAM     CONNECTED     4193649  /run/containerd/containerd.sock.ttrpc
unix  3      [ ]         STREAM     CONNECTED     20805    
unix  2      [ ]         DGRAM                    17150    
unix  3      [ ]         STREAM     CONNECTED     22222    
unix  3      [ ]         STREAM     CONNECTED     17156    
unix  3      [ ]         STREAM     CONNECTED     30178    /usr/local/aegis/Aegis-<Guid(5A2C30A2-A87D-490A-9281-6765EDAD7CBA)>
unix  3      [ ]         STREAM     CONNECTED     21969    
unix  3      [ ]         STREAM     CONNECTED     17693    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     21968    
unix  3      [ ]         STREAM     CONNECTED     21870    /run/dbus/system_bus_socket
unix  3      [ ]         STREAM     CONNECTED     30177    
unix  3      [ ]         STREAM     CONNECTED     17692    
unix  3      [ ]         STREAM     CONNECTED     21730    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     21728    
unix  3      [ ]         STREAM     CONNECTED     20097    
unix  3      [ ]         STREAM     CONNECTED     21869    
unix  3      [ ]         STREAM     CONNECTED     21970    
unix  3      [ ]         STREAM     CONNECTED     15213    
unix  3      [ ]         DGRAM                    14131    
unix  3      [ ]         STREAM     CONNECTED     14890    /run/systemd/journal/stdout
unix  2      [ ]         DGRAM                    13463    
unix  2      [ ]         DGRAM                    4887203  
unix  3      [ ]         STREAM     CONNECTED     4193148  /run/containerd/s/e33635bccc27c9922f596f19a760101c084a0faa8524dac77a39149af7e8e45e
unix  2      [ ]         DGRAM                    17752    
unix  3      [ ]         STREAM     CONNECTED     15214    
unix  2      [ ]         DGRAM                    11426    
unix  3      [ ]         STREAM     CONNECTED     4193144  
unix  3      [ ]         STREAM     CONNECTED     21008    
unix  2      [ ]         DGRAM                    17186    
unix  3      [ ]         STREAM     CONNECTED     21967    
unix  3      [ ]         DGRAM                    14132    
unix  3      [ ]         STREAM     CONNECTED     44479    
unix  3      [ ]         STREAM     CONNECTED     17252    
unix  2      [ ]         DGRAM                    15204    
unix  3      [ ]         STREAM     CONNECTED     13461    
unix  3      [ ]         STREAM     CONNECTED     20923    /run/systemd/journal/stdout
[root@iZwz90l6z42ze1mdxvb9giZ ~]# 
[root@iZwz90l6z42ze1mdxvb9giZ ~]# netstat -tunlp
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 192.168.1.5:47334       0.0.0.0:*               LISTEN      816/zerotier-one    
tcp        0      0 172.25.217.211:47334    0.0.0.0:*               LISTEN      816/zerotier-one    
tcp        0      0 192.168.1.5:9993        0.0.0.0:*               LISTEN      816/zerotier-one    
tcp        0      0 172.25.217.211:9993     0.0.0.0:*               LISTEN      816/zerotier-one    
tcp        0      0 127.0.0.1:9993          0.0.0.0:*               LISTEN      816/zerotier-one    
tcp        0      0 192.168.1.5:29994       0.0.0.0:*               LISTEN      816/zerotier-one    
tcp        0      0 172.25.217.211:29994    0.0.0.0:*               LISTEN      816/zerotier-one    
tcp        0      0 0.0.0.0:8082            0.0.0.0:*               LISTEN      13424/docker-proxy  
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1142/sshd           
tcp6       0      0 :::7000                 :::*                    LISTEN      809/frps            
tcp6       0      0 :::6000                 :::*                    LISTEN      809/frps            
udp        0   2560 192.168.1.5:47334       0.0.0.0:*                           816/zerotier-one    
udp        0      0 172.25.217.211:47334    0.0.0.0:*                           816/zerotier-one    
udp        0   2560 192.168.1.5:29994       0.0.0.0:*                           816/zerotier-one    
udp        0      0 172.25.217.211:29994    0.0.0.0:*                           816/zerotier-one    
udp        0      0 127.0.0.1:323           0.0.0.0:*                           473/chronyd         
udp        0   2560 192.168.1.5:9993        0.0.0.0:*                           816/zerotier-one    
udp        0      0 172.25.217.211:9993     0.0.0.0:*                           816/zerotier-one    
udp        0      0 0.0.0.0:68              0.0.0.0:*                           746/dhclient        
udp6       0      0 ::1:323                 :::*                                473/chronyd         
[root@iZwz90l6z42ze1mdxvb9giZ ~]# 
```



#### 1.4.8.5. 查看核心侦测的信息

核心会在开机过程中和运行时侦测的信息放置在内存的某一位置，用 `dmesg` 命令，可以查看这些信息。`dmesg` 可能产生很多的信息，建议使用 `|` 和 `more` 查看



```bash
[root@iZwz90l6z42ze1mdxvb9giZ ~]# dmesg | more
[    0.000000] Initializing cgroup subsys cpuset
[    0.000000] Initializing cgroup subsys cpu
[    0.000000] Initializing cgroup subsys cpuacct
[    0.000000] Linux version 3.10.0-862.14.4.el7.x86_64 (mockbuild@kbuilder.bsys.centos.org) (gcc version 4.8.5 20150623 (Red Hat 
4.8.5-28) (GCC) ) #1 SMP Wed Sep 26 15:12:11 UTC 2018
...
```





# 2. ~~SELinux~~



这块目前用不到先不学了

