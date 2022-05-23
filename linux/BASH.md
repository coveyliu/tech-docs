<!-- TOC -->

- [2.1. 功能](#21-%E5%8A%9F%E8%83%BD)
- [2.2. 查询指令是否是 Bash Shell 内建指令](#22-%E6%9F%A5%E8%AF%A2%E6%8C%87%E4%BB%A4%E6%98%AF%E5%90%A6%E6%98%AF-bash-shell-%E5%86%85%E5%BB%BA%E6%8C%87%E4%BB%A4)
- [2.3. 指令编辑](#23-%E6%8C%87%E4%BB%A4%E7%BC%96%E8%BE%91)
- [2.4. 操作环境](#24-%E6%93%8D%E4%BD%9C%E7%8E%AF%E5%A2%83)
    - [2.4.1. 进站和欢迎信息](#241-%E8%BF%9B%E7%AB%99%E5%92%8C%E6%AC%A2%E8%BF%8E%E4%BF%A1%E6%81%AF)
    - [2.4.2. Bash 环境配置（todo）](#242-bash-%E7%8E%AF%E5%A2%83%E9%85%8D%E7%BD%AEtodo)
- [3.1. 环境变量](#31-%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F)

<!-- /TOC -->

# 1. shell
<a id="markdown-shell" name="shell"></a>

- 什么是shell
- 你的 linux 支持哪些 shell？
- 使用者登录后，预设的 shell 是什么？
- bash shell 的功能有哪些？



kernel 控制着机器的软硬件。使用者可以通过 shell 给 kernel 下达指令来操作计算机

可以在 `/etc/shells` 目录下，查看你的机器支持哪些shell。

不同的使用者登录后，获取不同的 shell。`/etc/passwd` 中记载着每个用户登录后获取的 shell。比如下方：root 获取 /bin/bash shell，bin 用户获取的 shell 是 /sbin/nologin 类型的 shell

```bash
[root@covey ~]# cat /etc/shells
/bin/sh
/bin/bash
/usr/bin/sh
/usr/bin/bash
[root@covey ~]# cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
...
[root@covey ~]# 
```





# 2. Bash Shell
<a id="markdown-bash-shell" name="bash-shell"></a>



## 2.1. 功能
<a id="markdown-%E5%8A%9F%E8%83%BD" name="%E5%8A%9F%E8%83%BD"></a>

`/bin/bash` shell 是linux 预设的 shell，它兼容 `sh` shell。提供的功能包括：

1. 记录使用过命令（history）
   - `history -c` 清除
   - `!n` 执行 history 的第几条命令
2. tab 键补全命令或文件
3. 命令别名设定（alias）
   1. alias 查看别名
   2. 也可以设定别名。例如：使用 `lm` 替代 `alias lm ='ls -al'`
4. 工作控制
   - 可以使用前、背景执行 job。多任务执行非常方便
5. 通配符（wildcard）
   - 指令支持通配符。比如，查询 /usr/bin 以 X 开头的文件，`ls -l /usr/bin/X*`





## 2.2. 查询指令是否是 Bash Shell 内建指令
<a id="markdown-%E6%9F%A5%E8%AF%A2%E6%8C%87%E4%BB%A4%E6%98%AF%E5%90%A6%E6%98%AF-bash-shell-%E5%86%85%E5%BB%BA%E6%8C%87%E4%BB%A4" name="%E6%9F%A5%E8%AF%A2%E6%8C%87%E4%BB%A4%E6%98%AF%E5%90%A6%E6%98%AF-bash-shell-%E5%86%85%E5%BB%BA%E6%8C%87%E4%BB%A4"></a>



使用 `type` 命令，`type -[tpa] name`

- 不加任何选项会显示 `name` 是内建还是外部指令

- -t: 会显示一些文字。
  - file：外部指令
  - alias：别名命令
  - buitin：内建指令
- -p: 
- -a: 会将所有含有 name 的指令列都显示出来



```bash
[root@covey ~]# type cd
cd is a shell builtin
[root@covey ~]# type ls
ls is aliased to `ls --color=auto'
[root@covey ~]# type -t ls
alias
[root@covey ~]# type -a ls
ls is aliased to `ls --color=auto'
ls is /usr/bin/ls
[root@covey ~]# 
```



## 2.3. 指令编辑
<a id="markdown-%E6%8C%87%E4%BB%A4%E7%BC%96%E8%BE%91" name="%E6%8C%87%E4%BB%A4%E7%BC%96%E8%BE%91"></a>



- 在 shell 中输入的指令过长时，可以使用 `\` 换行，最后 `Enter` 执行指令
- <img width=80% src='https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220522154106.png'>



```bash
[root@covey ~]# cp hello.txt world.txt \
> liunx.txt /root/a
[root@covey ~]#
```





## 2.4. 操作环境
<a id="markdown-%E6%93%8D%E4%BD%9C%E7%8E%AF%E5%A2%83" name="%E6%93%8D%E4%BD%9C%E7%8E%AF%E5%A2%83"></a>



### 2.4.1. 进站和欢迎信息
<a id="markdown-%E8%BF%9B%E7%AB%99%E5%92%8C%E6%AC%A2%E8%BF%8E%E4%BF%A1%E6%81%AF" name="%E8%BF%9B%E7%AB%99%E5%92%8C%E6%AC%A2%E8%BF%8E%E4%BF%A1%E6%81%AF"></a>



- 从终端机接口（tty1 - tty6)登录时，会显示一些信息，这些信息在 `/etc/issue` 中
- 用 `telnet` 登录程序进去的看到的信息，在 `/etc/issue.net` 中
- 每个使用者登录后会看到一些信息，这些信息在 `/etc/motd`



`issue` 中各代码的意义，（`issue` 中可以利用 `\` 读取变量，这些变量的内容如下）

- `\d` 本地日期
- `\l` 显示第几个终端机接口
- `\m` 显示硬件等级
- `\n` 显示主键的网络名称
- `\O` 显示 domain name
- `\r` 显示操作系统版本（相当于 uname -r）
- `\t` 显示本地时间
- `\S`显示操作系统的名称
- `\v` 显示操作系统版本

<img width=60% src='https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220523085724.png'>



```bash
WARNING! The remote SSH server rejected X11 forwarding request.
Last login: Mon May 23 08:41:00 2022 from 58.35.54.216

Welcome to Alibaba Cloud Elastic Compute Service !

[root@covey ~]# cd ~
[root@covey ~]# cat /etc/issue
\S
Kernel \r on an \m

[root@covey ~]# cat /etc/motd 

Welcome to Alibaba Cloud Elastic Compute Service !

[root@covey ~]# cat /etc/issue.net 
\S
Kernel \r on an \m
[root@covey ~]# 
WARNING! The remote SSH server rejected X11 forwarding request.
Last login: Mon May 23 08:41:00 2022 from 58.35.54.216

Welcome to Alibaba Cloud Elastic Compute Service !

[root@covey ~]# cd ~
[root@covey ~]# cat /etc/issue
\S
Kernel \r on an \m

[root@covey ~]# cat /etc/motd 

Welcome to Alibaba Cloud Elastic Compute Service !

[root@covey ~]# cat /etc/issue.net 
\S
Kernel \r on an \m
[root@covey ~]# 
```





### 2.4.2. Bash 环境配置（todo）
<a id="markdown-bash-%E7%8E%AF%E5%A2%83%E9%85%8D%E7%BD%AE%EF%BC%88todo%EF%BC%89" name="bash-%E7%8E%AF%E5%A2%83%E9%85%8D%E7%BD%AE%EF%BC%88todo%EF%BC%89"></a>

- shell 分两种 `login-shell`, `no-llogin shell`
- 每个用户登录后，都会获取一个 bash 环境









# 3. shell 变量
<a id="markdown-shell-%E5%8F%98%E9%87%8F" name="shell-%E5%8F%98%E9%87%8F"></a>



## 3.1. 环境变量
<a id="markdown-%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F" name="%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F"></a>



有些变量是被导入到系统中的。这些变量称为 **环境变量**，环境变量名一般大写。每个bash环境都可以读取到这些变量



**示例**

- `env` 读取所有环境变量
- `set` 读取当前 bash 环境的所有的变量，包括环境变量
- `export` 将自定义变量转为环境变量
  - **注意**：export 后的变量只能在当前 bash 和子 bash 中使用



```bash
[root@covey ~]# env
XDG_SESSION_ID=1538
HOSTNAME=covey
TERM=xterm
SHELL=/bin/bash
HISTSIZE=1000
SSH_CLIENT=58.35.54.216 12462 22
SSH_TTY=/dev/pts/0
USER=root
... # 省略
[root@covey ~]# set
BASH=/bin/bash
... # 省略
name=covey
[root@covey ~]# # 进入子程序bash，查看 name 变量
[root@covey ~]# bash
[root@covey ~]# echo $name

[root@covey ~]# # 退出子 bash
[root@covey ~]# exit
exit
[root@covey ~]# # 将 name 转为环境变量
[root@covey ~]# export name
[root@covey ~]# # 再次进入子 bash
[root@covey ~]# bash
[root@covey ~]# # 输出 name 内容
[root@covey ~]# echo $name
covey
[root@covey ~]# 
[root@covey ~]# # 退出
[root@covey ~]# exit
exit
[root@covey ~]# # 删除变量 name
[root@covey ~]# unset name
[root@covey ~]# echo $name

[root@covey ~]# 
```







