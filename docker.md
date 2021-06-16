# 第一章 Docker

## 1.1 安装

根据官网安装

### 1.1.1 查询镜像

```js
docker search [name]
```

## 1.2 镜像下载

DockerHub

* 官方centos镜像7

```JS
docker pull centos:7
```

## 1.3 功能

### 1.3.1 查看运行

```JS
docker ps -a
```

### 1.3.2 端口映射

```shell
docker run -p [port]:[port] [Name]
```

### 1.3.3 文件传输

```shell
 docker cp 本地文件路径 ID全称:容器路径
 docker cp ID全称:容器路径 本地文件路径 
```

### 1.3.4 网络
  
```shell
docker network list   # 查看当前network
docker network inspect bridge # 查看对应network 对应网络的设置
```

### 1.3.5 提交

```shell
docker commit [Name] [NewImageName]
```

### 1.3.6 删除

```shell
docker rm [Id]
```

### 1.3.7 更新配置
  
  [更新Docker配置的四种方法](https://bobcares.com/blog/docker-change-container-configuration/)

1. 重新发布

```shell

docker stop A
docker commit A imageA #将容器commit提交成为一个镜像
docker rm A #删除原镜像
docker run -d -p 80:80 --name A imageA #启动新镜像
```

## 1.4 问题

### 1.4.1 docker容器已启动就结束问题

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
docker run -itd -p 2181:2181 -p 8080:8080 -p 8085:8085 -p 9090:9090 -p 9095:9095 -p 16000:16000 -p 16010:16010 -p 16201:16201 -p 16301:16301 jaikensai888/centos7-hbase-phoenix-lite:v1 /usr/sbin/init


docker run -itd --name centos7 centos:7
```

### 1.4.2 docker 宿主机ping不通容器

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

### 1.4.3 Windows

>https://docs.docker.com/docker-for-windows/networking/

There is no docker0 bridge on Windows
Because of the way networking is implemented in Docker Desktop for Windows, you cannot see a docker0 interface on the host. This interface is actually within the virtual machine.

I cannot ping my containers
Docker Desktop for Windows can’t route traffic to Linux containers. However, you can ping the Windows containers.

### 1.4.4 MAC

> https://docs.docker.com/docker-for-mac/networking/#there-is-no-docker0-bridge-on-macos
> 
There is no docker0 bridge on macOS🔗
Because of the way networking is implemented in Docker Desktop for Mac, you cannot see a docker0 interface on the host. This interface is actually within the virtual machine.

Docker Desktop for Mac can’t route traffic to containers

I cannot ping my containers
Docker Desktop for Mac can’t route traffic to containers.