

<!-- TOC -->

- [1.1. 安装并配置](#11-%E5%AE%89%E8%A3%85%E5%B9%B6%E9%85%8D%E7%BD%AE)
- [1.2. 创建新用户并赋予权限](#12-%E5%88%9B%E5%BB%BA%E6%96%B0%E7%94%A8%E6%88%B7%E5%B9%B6%E8%B5%8B%E4%BA%88%E6%9D%83%E9%99%90)
- [1.3. 参考](#13-%E5%8F%82%E8%80%83)

<!-- /TOC -->

# 1. 安装 mysql
<a id="markdown-%E5%AE%89%E8%A3%85-mysql" name="%E5%AE%89%E8%A3%85-mysql"></a>



## 1.1. 安装并配置
<a id="markdown-%E5%AE%89%E8%A3%85%E5%B9%B6%E9%85%8D%E7%BD%AE" name="%E5%AE%89%E8%A3%85%E5%B9%B6%E9%85%8D%E7%BD%AE"></a>



```
wget https://dev.mysql.com/get/mysql80-community-release-el7-6.noarch.rpm

yum localinstall mysql80-community-release-el7-6.noarch.rpm
 
systemctl start mysqld
 
systemctl status mysqld
 
grep 'temporary password' /var/log/mysqld.log
 
mysql -uroot -p
 
ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass4!';
```



**配置 /etc/my.cnf**

主要配置 sever-id 项

```cnf
[mysqld]
server-id=1
...(省略)
```





## 1.2. 创建新用户并赋予权限
<a id="markdown-%E5%88%9B%E5%BB%BA%E6%96%B0%E7%94%A8%E6%88%B7%E5%B9%B6%E8%B5%8B%E4%BA%88%E6%9D%83%E9%99%90" name="%E5%88%9B%E5%BB%BA%E6%96%B0%E7%94%A8%E6%88%B7%E5%B9%B6%E8%B5%8B%E4%BA%88%E6%9D%83%E9%99%90"></a>



```mysql
mysql> SHOW VARIABLES LIKE 'validate_password%';
+--------------------------------------+-------+
| Variable_name                        | Value |
+--------------------------------------+-------+
| validate_password.check_user_name    | ON    |
| validate_password.dictionary_file    |       |
| validate_password.length             | 8     |
| validate_password.mixed_case_count   | 1     |
| validate_password.number_count       | 1     |
| validate_password.policy             | LOW   |
| validate_password.special_char_count | 1     |
+--------------------------------------+-------+
7 rows in set (0.10 sec)

mysql>  set global validate_password.policy=0; -- 设置低级别密码校验规则
Query OK, 0 rows affected (0.00 sec)

mysql>  set global validate_password.length=0; -- 设置低级别密码长度
Query OK, 0 rows affected (0.00 sec)


mysql> create user canal@'%' identified with mysql_native_password by 'canal';
grant select, replication slave, replication client on *.* to `canal`@'%'; -- 创建 user
Query OK, 0 rows affected (0.01 sec)
Query OK, 0 rows affected (0.00 sec)

mysql> 
```





## 1.3. 参考
<a id="markdown-%E5%8F%82%E8%80%83" name="%E5%8F%82%E8%80%83"></a>



> 参考
>
> 1. 安装文档：https://dev.mysql.com/doc/refman/8.0/en/linux-installation-yum-repo.html
> 2. default startup configuration file：https://dev.mysql.com/doc/refman/8.0/en/option-files.html
> 3. 配置文件说明：https://dev.mysql.com/doc/refman/8.0/en/option-files.html
> 4. 编写配置文件：https://dev.mysql.com/doc/refman/8.0/en/command-line-options.html





# 2. 安装 canal
<a id="markdown-%E5%AE%89%E8%A3%85-canal" name="%E5%AE%89%E8%A3%85-canal"></a>





```
wget ‐‐no‐check‐certificate https://github.com/alibaba/canal/releases/download/canal-1.1.6/canal.deployer-1.1.6.tar.gz

tar -zxvf canal-1.1.6/canal.deployer-1.1.6.tar.gz ‐C /usr/local/canal
 
cd /usr/local/canal
```



- 修改配置

  修改实例 `instance` 的 数据库配置

  ```
  vi conf/example/instance.properties
  ```


  ```
  #position info，需要改成自己的数据库信息
  canal.instance.master.address = 127.0.0.1:3306 
  canal.instance.master.journal.name = 
  canal.instance.master.position = 
  canal.instance.master.timestamp = 
  #canal.instance.standby.address = 
  #canal.instance.standby.journal.name =
  #canal.instance.standby.position = 
  #canal.instance.standby.timestamp = 
  #username/password，需要改成自己的数据库信息
  canal.instance.dbUsername = canal  
  canal.instance.dbPassword = canal
  canal.instance.defaultDatabaseName =
  canal.instance.connectionCharset = UTF-8
  ```



- 启动

  ```
  ./bin/startup.sh
  ```



- 查看 canal server 日志

  ```
  tail logs/canal/canal.log
  ```

  

- 查看 instance 日志

  ```
  tail logs/example/example.log
  ```

  