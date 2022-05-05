# 常见工具安装

以下工具均在Ubuntu20.04下安装

## 1 FTP

### 1.1 安装vsftpd

```
sudo apt-get install vsftpd
```

如果出现下面的情况

```
Reading package lists... Done
Building dependency tree
Reading state information... Done
E: Unable to locate package vsftp
```

可进行一下操作

```
sudo apt-get clean
sudo apt-get upgrade
sudo apt-get update
```

### 1.2 修改/etc/vsftpd.conf

```
chroot_list_enable=YES
# (default follows)
# 白名单用户，注：只需要列出用户名即可，每个用户占一行，没有该文件自己创建即可
chroot_list_file=/etc/vsftpd.chroot_list
allow_writeable_chroot=YES

secure_chroot_dir=/var/run/vsftpd/empty
#
# This string is the name of the PAM service vsftpd will use.
# 如果ubuntu系统需要将pam_service_name修改为ftp, centos则不用修改
pam_service_name=ftp
```

### 1.3 添加ftp用户

```
# 创建一个文件夹用于接收ftp传输的数据
mkdir /home/junzhu

# 创建junzhu用户, 同时设置junzhu不能登录系统(nologin)
useradd -d /home/junzhu -s /sbin/nologin junzhu

# 修改该文件夹的权限
chmod u+rx /home/junzhu

# 修改用户zhangsan的密码
passwd junzhu
```

用户junzhu不能使用此账户登录系统, 记得在vsftpd.chroot_list中添加用户白名单，只需要列出用户名即可

### 1.4 重启ftp服务

service vsftpd restart

### 1.5 测试

```
C:\Users\Administrator>ftp 127.0.0.1
连接到 127.0.0.1。
220 (vsFTPd 3.0.3)
200 Always in UTF8 mode.
用户(127.0.0.1:(none)): junzhu
331 Please specify the password.
密码:
230 Login successful.
ftp>
```

## 2 JDK

### 2.1 解压工具包

```shell
# 创建java目录
cd /usr/local/
sudo mkdir java
	
# 解压压缩包
sudo tar -zxvf jdk-8u301-linux-x64.tar.gz -C /usr/local/java/
```

### 2.2 配置系统变量

vim /etc/profile

```shell
# JDK 8
export JAVA_HOME=/usr/local/java/jdk1.8.0_301
export JRE_HOME=$JAVA_HOME/jre
export CLASSPATH=$JAVA_HOME/lib:$JRE_HOME/lib
export PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin

# JDK 11
export JAVA_HOME=/usr/local/java/jdk-11.0.12
export CLASSPATH=$JAVA_HOME/lib
export PATH=$PATH:$JAVA_HOME/bin
```

使得刚才的配置生效

**source /etc/profile**

### 2.3 验证是否安装成功

```shell
java -version

like this...

java version "1.8.0_241"
Java(TM) SE Runtime Environment (build 1.8.0_241-b07)
Java HotSpot(TM) 64-Bit Server VM (build 25.241-b07, mixed mode)
```



## 3 MySQL

### 3.1 安装软件包

```shell
sudo apt update
# ubuntu默认安装的是MySQL8.0+
sudo apt install mysql-server
# 查看当前mysql服务状态
sudo systemctl status mysql
```

### 3.2 初始化密码

```mysql
sudo mysql

# 更改mysql的默认root用户密码
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'xjz99076'; 

# 更新权限
FLUSH PRIVILEGES;
# 退出mysql
quit; 
```

### 3.3 修改配置文件

```shell
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
```

### 3.4 重启MySQL服务

```shell
# 重启服务
sudo service mysql restart
```

### 3.6 远程连接设置

```mysql
# mysql 8.0+ 使用如下指令
create user root@'%' identified by '123456';
 
grant all privileges on *.* to root@'%' with grant option;
```

### 3.5 MySQL的卸载

```mysql
# 首先在终端中查看MySQL的依赖项：dpkg --list|grep mysql
CREATE DATABASE IF NOT EXISTS guli DEFAULT CHARSET utf8 COLLATE utf8_general_ci;
# 卸载： sudo apt-get remove mysql-common

# 卸载：sudo apt-get autoremove --purge mysql-server-5.7

# 清除残留数据：dpkg -l|grep ^rc|awk '{print$2}'|sudo xargs dpkg -P

# 再次查看MySQL的剩余依赖项：dpkg --list|grep mysql

# 继续删除剩余依赖项，如：sudo apt-get autoremove --purge mysql-apt-config
```



## 4 Redis

### 4.1 解压文件

```shell
tar -zxvf redis-6.0.6.tar.gz
```

### 4.2 编译

```shell
# 在redis-6.0.6目录里执行, 执行之前确保系统中有gcc环境
make

make install
```

### 4.3 配置redis.conf

```shell
# 默认安装的位置再
/usr/local/bin

# 可以将redis-6.0.6中的配置文件复制出一份放在/etc/redis.conf
#以守护进程运⾏
daemonize yes
# 注释调绑定ip
# bind 127.0.0.1
# 关闭保护模式, 可以远程连接
protected-mode no
```

### 4.4 启动

```shell
./redis-server /etc/redis.conf
```



## 5 RabbitMQ

### 5.1 安装RabbitMQ环境

由于RabbitMQ需要erlang语言的支持，在安装rabbitMq之前需要安装erlang，执行命令：

```shell
# 安装Erlang环境
sudo apt-get install erlang-nox    # 安装erlang
# 查看ErLang的版本
erl    # 查看relang语言版本，成功执行则说明relang安装成功
```

###  5.2 安装 RabbitMQ

```shell
sudo apt-get install rabbitmq-server  #安装成功自动启动

# 查看RabbitMQ的状态
systemctl status rabbitmq-server
```



RabbitMQ的启动与关闭

```shell
service rabbitmq-server start    # 启动

service rabbitmq-server stop    # 停止

service rabbitmq-server restart  # 重启
```

### 5.3 Web可视化操作界面

```shell
# 启用插件

rabbitmq-plugins enable rabbitmq_management

# 装完后重启 

service rabbitmq-server restart  
```



```shell
# 查看rabbitmq用户
rabbitmqctl list_users
```

### 5.4 账号管理

```shell
# 增加普通用户
rabbitmqctl add_user admin 123 

# 给普通用户分配管理员角色
rabbitmqctl set_user_tags admin administrator   

# 设置非主机课访问
rabbitmqctl set_permissons -p "/" ".*" ".*" ".*"

# 最后就可以使用admin访问 http://192.168.0.104:15672/
```

