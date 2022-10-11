# Linux基础工具安装

## 版本说明

```shell
centos:yum
ubuntu:apt-get
alpine:apk
```

## 命令

### 程序指令

```shell
ps -ef |grep [name] #根据查询运行程序
ll /proc/PID # 根据Pid查询程序运行绝对路径
kill 进程Id  #杀死进程
```

### 压缩与解压

```shell
--- 压缩
tar –cvf jpg.tar *.jpg  将目录里所有jpg文件打包成tar.jpg
tar –czf jpg.tar.gz *.jpg   将目录里所有jpg文件打包成jpg.tar后，并且将其用gzip压缩，生成一个gzip压缩过的包，命名为jpg.tar.gz
tar –cjf jpg.tar.bz2 *.jpg 将目录里所有jpg文件打包成jpg.tar后，并且将其用bzip2压缩，生成一个bzip2压缩过的包，命名为jpg.tar.bz2
tar –cZf jpg.tar.Z *.jpg   将目录里所有jpg文件打包成jpg.tar后，并且将其用compress压缩，生成一个umcompress压缩过的包，命名为jpg.tar.Z
rar a jpg.rar *.jpg rar格式的压缩，需要先下载rar for linux
zip jpg.zip *.jpg   zip格式的压缩，需要先下载zip for linux

---解压
tar –xvf file.tar  解压 tar包
tar -xzvf file.tar.gz 解压tar.gz
tar -xjvf file.tar.bz2   解压 tar.bz2
tar –xZvf file.tar.Z   解压tar.Z
unrar e file.rar 解压rar
unzip file.zip 解压zip
```

### 网络端口

```shell
netstat -tunlp ：端口占用情况
netstat -tlnp | grep :22  ：指定端口查看
```

### 进程端口连接情况

```shell
lsof -a -i tcp:7001 -P -R -l  #查看端口进程占用情况
```

### 查看linux版本

```shell
cat /proc/version
uname -a
```

### 设置环境变量（全局变量）

```shell
# 
vi /etc/profile #  全局环境变量
vi ~/.bash_profile # 用户当前环境变量
export PATH=  #增加到path后,可以直接执行；
export JAVA_HOME=/usr/bin/java # 例子

source ~/.bash_profile # 生效问题;

echo $JAVA_HOME # 查看是否已经设置成功
```

### 文件系统

```shell
find / -name *tomcat*   # 查询文件位置
```

### 防火墙

firewalld

```shell
systemctl status firewalld                     #查看firewall防火墙状态
systemctl start firewalld.service            #打开firewall防火墙
systemctl stop firewalld.service            #关闭firewall防火墙
systemctl disable firewalld.service            #禁止firewall开机启动  
firewall-cmd --query-port=6379/tcp        #查询指定端口是否开启成功
firewall-cmd --list-ports                    #查看firewall防火墙开放端口
firewall-cmd --reload                        #重启firewal防火墙
firewall-cmd --remove-port=123/tcp  --permanent 
firewall-cmd --zone=public --add-port=80/tcp --permanent 


移除指定端口：

命令含义:
–zone #作用域
–add-port=80/tcp #添加端口，格式为：端口/通讯协议
–permanent #永久生效，没有此参数重启后失效
```

iptable

```shell
 iptables -nvL --line-number # 查看防火墙规则
 iptables -L -n
 iptables -I IN_public_allow -s 192.168.1.8 -j ACCEPT # 新增防火墙进入规则
 iptables -I IN_public_allow -s 192.168.1.8 -j ACCEPT
```

### 安全策略

```shell
setenforce 0 #设置为primission状态
sestatus #查看状态
vim /etc/selinux/config
```

### 查看进程是否运行

```shellsession
ps -A | grep nginx
```

## 工具安装与使用

* jdk 安装

详细参考（验证可行:通常使用jdk8/因为基本兼容）

> https://www.cnblogs.com/yaun1498078591/p/10368884.html

```shell
yum search java|grep jdk
yum install java-1.8.0-openjdk
yum install java-1.8.0-openjdk-devel.x86_64
```

* 指令 ifConfig

```shell
# 安装
yum install net-tools 
# 使用
ifconfig -all  
```

* vi 工具

```shell
vi 文件 ：编辑文件
按 [i] 进入编辑模式  
按[Esc]退出编辑模式 
:wq退出且保存
:q!退出放弃保存
:q 退出
```

* ssh
  yum  install openssh-clients
