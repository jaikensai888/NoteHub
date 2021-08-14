# Docker

##  1、简介

> Docker 分成了2个版本Docker CE（免费开发版本） 与Docker EE （企业使用版本）

## 2、安装

> 官网Windows DeskTop 对标DockerCE 使用与Window10

* windowServer 安装教程

  [正式版](https://docs.microsoft.com/zh-cn/virtualization/windowscontainers/quick-start/set-up-environment?tabs=Windows-Server) （只能使用windows容器）

  [预览版](https://bbs.huaweicloud.com/blogs/117189) （才可以切换linux容器与docker容器）

###  2.1、查询镜像

```js
docker search [name]
```

##  3、镜像下载

DockerHub

* 官方centos镜像7

```JS
docker pull centos:7
```

## 4、功能

###  4.1、查看运行

```JS
docker ps -a
```

### 4.2、切换内核

> windowServer 只有预览版本可以
>
> windows 10  的CE版本可以

```javascript
切换到Linux内核容器
>[Environment]::SetEnvironmentVariable("LCOW_SUPPORTED", "1", "Machine")
>Restart-Service Docker

切换到Windows内核容器
>[Environment]::SetEnvironmentVariable("LCOW_SUPPORTED", $null, "Machine")
>Restart-Service Docker
```

### 4.3、端口映射

```shell
docker run -p [port]:[port] [Name]
```

###  4.4、文件传输

```shell
 docker cp 本地文件路径 ID全称:容器路径
 docker cp ID全称:容器路径 本地文件路径 
```

### 4.5、网络

```shell
docker network list   # 查看当前network
docker network inspect bridge # 查看对应network 对应网络的设置
```

### 4.6、提交

```shell
docker commit [Name] [NewImageName]
```

###  4.7、删除

```shell
docker rm [Id]
```

###  4.8、更新配置

  [更新Docker配置的四种方法](https://bobcares.com/blog/docker-change-container-configuration/)

1. 重新发布

```shell

docker stop A
docker commit A imageA #将容器commit提交成为一个镜像
docker rm A #删除原镜像
docker run -d -p 80:80 --name A imageA #启动新镜像
```

##  5、问题

### 5.1、docker容器已启动就结束问题

>原因docker 没有执行相关前台运行命令会自动关闭

错误实例

```shell
docker run -p centos：centos7
```

解决方案

```shell
docker run -itd centos:centos7 /usr/sbin/init
docker run -dit centos /bin/bash
docker run -itd --privileged centos:centos7 /usr/sbin/init
docker run -itd --privileged -p 2181:2181 -p 8080:8080 -p 8085:8085 -p 9000:9000 -p 9095:9095 -p 16000:16000 -p 16010:16010 -p 16201:16201 -p 16301:16301  -p 16030:16030 -p 16020:16020 --name hbase002 jaikensai888/centos7-hbase-phoenix-lite:v4 /usr/sbin/init


docker run -itd --name centos7 centos:7
```

### 5.2、docker 宿主机ping不通容器

问题描述
>前提使用默认模式时：docker 的桥接

```shell
--- #前提
windows 宿主机 网卡不存在docker0
虚拟网卡只有 

vEth(Default Switch) 172.25.240.1 255.255.240.0  13
vEth(nat)  192.168.128.1 255.255.240.0 26
vEth (wsl) 172.29.192.1 255.255.0.0   32

docker briage
contain ip=172.17.0.3 255.255.0.0 

docker subnet Setting
192.168.65.0/28

--- #测试1

route add 172.17.0.0/16 mask 255.255.0.0 172.17.0.1 if 26

172.17.0.0      255.255.0.0       172.17.0.1    192.168.128.1   5001
false

route add 172.17.0.0/16 mask 255.255.0.0 172.17.0.1 if 1
172.17.0.0      255.255.0.0       172.17.0.1        127.0.0.1     76
一般故障


```

解决方法

没有办法解决

###  5.3、Windows

>https://docs.docker.com/docker-for-windows/networking/

There is no docker0 bridge on Windows
Because of the way networking is implemented in Docker Desktop for Windows, you cannot see a docker0 interface on the host. This interface is actually within the virtual machine.

I cannot ping my containers
Docker Desktop for Windows can’t route traffic to Linux containers. However, you can ping the Windows containers.

###  5.4、MAC

> https://docs.docker.com/docker-for-mac/networking/#there-is-no-docker0-bridge-on-macos
> 
There is no docker0 bridge on macOS🔗
Because of the way networking is implemented in Docker Desktop for Mac, you cannot see a docker0 interface on the host. This interface is actually within the virtual machine.

Docker Desktop for Mac can’t route traffic to containers

I cannot ping my containers
Docker Desktop for Mac can’t route traffic to containers.