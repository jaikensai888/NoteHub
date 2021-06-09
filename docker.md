# Docker

## 1.安装

根据官网安装

* 查询镜像

```js
docker search [name]
```

## 2.镜像下载

DockerHub

* 官方centos镜像7

```JS
docker pull centos:7
```

## 3.功能

* 查看运行

```JS
docker ps -a
```

* 端口映射

```shell
docker run -p [port]:[port] [Name]
```

* 文件传输

```shell
 docker cp 本地文件路径 ID全称:容器路径
 docker cp ID全称:容器路径 本地文件路径 
```

* 提交

```shell
docker commit [Name] [NewImageName]
```

* 删除

```shell
docker rm [Id]
```

* 重新发布

```shell

docker stop A
docker commit A imageA #将容器commit提交成为一个镜像
docker rm A #删除原镜像
docker run -d -p 80:80 --name A imageA #启动新镜像
```

## 4. 问题

* docker容器已启动就结束问题

>原因docker 没有执行相关前台运行命令会自动关闭

错误实例

```shell
docker run -p centos：centos7
```

解决方案

```shell
docker run -itd centos:centos7 /usr/sbin/init
docker run -dit centos /bin/bash

# docker run -itd --name centos7 centos:7
```

* docker 宿主机ping不通容器

问题描述
>前提使用默认模式时：docker 的桥接

解决方法
> https://blog.51cto.com/zhongliang/2454181