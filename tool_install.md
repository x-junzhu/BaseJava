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

