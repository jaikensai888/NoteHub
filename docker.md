# Docker

## 1.安装

根据官网安装

* 查询镜像

```
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

```
docker run -p [port]:[port] [Name]

```

* 文件传输

```
 docker cp 本地文件路径 ID全称:容器路径
 docker cp ID全称:容器路径 本地文件路径 
```

* 提交

```
docker commit [Name] [NewImageName]
```

* 删除

```
docker rm [Id]
```

* 重新发布

```

docker stop A
docker commit A imageA #将容器commit提交成为一个镜像
docker rm A #删除原镜像
docker run -d -p 80:80 --name A imageA #启动新镜像
```

## 4. 问题

* docker容器已启动就结束问题

>原因docker 没有执行相关前台运行命令会自动关闭
>错误实例
```error example

docker run -p centos：centos7

```

>解决方案

```js
docker run -itd centos:centos7 /usr/sbin/init
docker run -dit centos /bin/bash

```
