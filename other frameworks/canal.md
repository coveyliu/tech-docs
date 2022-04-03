# mysql



1、安装 mysql，请自行安装mysql

```sh
# Linux平台上推荐使用RPM包

wget http://repo.mysql.com/mysql80-community-release-el7-5.noarch.rpm

# 解决本地的 rpm 安装包的依赖关系（会自动下载本地 rpm 需要的依赖）
# yum install 
yum localinstall -y mysql80-community-release-el7-5.noarch.rpm

# 安装
# 其中 -i 为安装命令参数，-v 为安装过程中显示正在安装文件信息的命令参数，-h 显示安装进度的命令参数，## 三者一起使用即为安装xxx.rpm并在安装过程中显示正在安装文件信息及安装进度。
rpm -ivh mysql80-community-release-el7-5.noarch.rpm

yum install -y mysql-community-server

# 获取初始密码
grep 'temporary password' /var/log/mysqld.log
```



​	**注意修改配置文件**

```sh
sudo cat >> /etc/mysql/my.cnf << EOF
server‐id=1
log‐bin=mysql‐bin
# canal 支持 row 格式的 binlog
binlog_format=row
# binlog‐do‐db=world # 这个不需要配置
EOF
```



2、创建新用户

- 进入 mysql , mysql -u root -p 

- 创建新用户，create user canal@'%' identified with mysql_native_password by 'canal';

- 授予 canal 用户权限

  ```sh
  grant select, replication slave, replication client on *.* to `canal`@'%';
  ```

  



# 安装 canal-server

1、 安装 JDK

```sh
yum ‐y install java‐1.8.0‐openjdk‐devel.x86_64
sudo cat >> /etc/profile <<‐'EOF'
export JAVA_HOME=/usr/lib/jvm/java‐1.8.0‐openjdk
export JRE_HOME=$JAVA_HOME/jre
export CLASSPATH=$JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH
export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$PATH
EOF
source /etc/profile
echo $JAVA_HOME
```



2、下载 canal 最新版

```sh
# 下载
wget ‐‐no‐check‐certificate https://manongbiji.oss‐cn‐beijing.aliyuncs.com/ittailkshow/canal/download/canal.deployer‐1.1.5.tar.gz

# 解压到 /home/canal
mkdir -p /usr/local/canal
tar zxvf canal.deployer‐1.1.5.tar.gz ‐C /usr/local/canal & cd /usr/local/canal

# 修改 canal 配置文件
vi conf/example/instance.properties
# 调整serverId
canal.instance.mysql.slaveId=10
# master地址
canal.instance.master.address=192.168.1.1:3306
# 关闭tsdb
canal.instance.tsdb.enable=false
#确认canal同步用的用户名、密码
canal.instance.dbUsername=canal
canal.instance.dbPassword=canal

# 启动服务
sh bin/startup.sh

# 有些端口，需要放开，请自行使用
#canal admin 端口 firewall‐cmd ‐‐zone=public ‐‐add‐port=11110/tcp ‐‐permanent
#canal 监听端口
#firewall‐cmd ‐‐zone=public ‐‐add‐port=11111/tcp ‐‐permanent
#canal 指标监控端口
#firewall‐cmd ‐‐zone=public ‐‐add‐port=11112/tcp ‐‐permanent
#firewall‐cmd ‐‐reload

# 查看日志，检查 canal server 是否启动成功
tail canal.log
```







# 开发 client 程序



**引入依赖**

```xml
<dependencies>
    <dependency>
        <groupId>com.alibaba.otter</groupId>
        <artifactId>canal.client</artifactId>
        <version>1.1.5</version>
    </dependency>
    <dependency>
        <groupId>com.alibaba.otter</groupId>
        <artifactId>canal.protocol</artifactId>
        <version>1.1.5</version>
    </dependency>
</dependencies>
```



`CanalDemo.java`

```java
// todo
public class CanalDemo{
    
    
    
}
```

