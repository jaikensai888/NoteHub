# Nginx

## 1.端口转发

```shell
    # vmsApi
        server {
        gzip on;
        gzip_buffers 32 4K;
        gzip_comp_level 6;
        gzip_min_length 100;
        gzip_types application/javascript text/css text/xml;
        gzip_disable "MSIE [1-6]\."; #配置禁用gzip条件，支持正则。此处表示ie6及以下不启用gzip（因为ie低版本不支持）
        gzip_vary on;
        listen       8088;
        server_name  gps.jetone.cn;
        location / {
            proxy_pass http://47.106.210.24:8088;
        }
    }
```

## 2.https

```shell
    server {
        listen       8103 ssl;
        server_name  gps.jetone.cn;
        ssl_certificate      D:\SSL\STAR_jetone_cn.crt;
        ssl_certificate_key  D:\SSL\ssl.key;
        ssl_session_cache    shared:SSL:1m;
        ssl_session_timeout  5m;

        ssl_ciphers  HIGH:!aNULL:!MD5;
        ssl_prefer_server_ciphers  on;

        location / {
            proxy_pass http://59.36.239.170:28100;
        }
    }
```

## 3.wss

```shell
    #VmsSocket
    server {
        listen       8092 ssl;
        server_name  gps.jetone.cn;
        ssl_certificate      D:\SSL\STAR_jetone_cn.crt;
        ssl_certificate_key  D:\SSL\ssl.key;
        ssl_session_cache    shared:SSL:1m;
        ssl_session_timeout  5m;

        ssl_ciphers  HIGH:!aNULL:!MD5;
        ssl_prefer_server_ciphers  on;

        location / {
            proxy_pass http://192.168.1.85:8092;
            proxy_read_timeout 60s;
            proxy_set_header Host $host;
            proxy_set_header X-Real_IP $remote_addr;
            proxy_set_header X-Forwarded-for $remote_addr;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection 'Upgrade';
        }
    }
```

## 4.相同端口公用域名区分

```shell
    #Test
    server {
        gzip on;
        gzip_buffers 32 4K;
        gzip_comp_level 6;
        gzip_min_length 100;
        gzip_types application/javascript text/css text/xml;
        gzip_disable "MSIE [1-6]\."; #配置禁用gzip条件，支持正则。此处表示ie6及以下不启用gzip（因为ie低版本不支持）
        gzip_vary on;
        listen       8108;
        server_name  bss.jetone.cn;
        location / {
            proxy_pass http://192.168.1.85:8101;
        }
    }
    server {
        gzip on;
        gzip_buffers 32 4K;
        gzip_comp_level 6;
        gzip_min_length 100;
        gzip_types application/javascript text/css text/xml;
        gzip_disable "MSIE [1-6]\."; #配置禁用gzip条件，支持正则。此处表示ie6及以下不启用gzip（因为ie低版本不支持）
        gzip_vary on;
        listen       8108;
        server_name  59.36.239.104;
        location / {
            proxy_pass http://192.168.1.85:8089;
        }
    }n:8113;
        }
    }
```

## 5.流量复制

参考文档：[Nginx 流量镜像使用技巧 - 腾讯云开发者社区-腾讯云](https://cloud.tencent.com/developer/article/1495449)

```shell
    # vmsSocket
        server {
        gzip on;
        gzip_buffers 32 4K;
        gzip_comp_level 6;
        gzip_min_length 100;
        gzip_types application/javascript text/css text/xml;
        gzip_disable "MSIE [1-6]\."; #配置禁用gzip条件，支持正则。此处表示ie6及以下不启用gzip（因为ie低版本不支持）
        gzip_vary on;
        listen       8091;
        server_name  gps.jetone.cn;
        location / {
            mirror /mirror;
            proxy_pass http://192.168.1.85:8091;
        }
        location = /mirror {
            internal;
            proxy_pass http://app.jetone.cn:8113$request_uri;
        }
    }
```

## 6.负载均衡

参考文献[Nginx的upstream详解 - 简书](https://www.jianshu.com/p/8671c40a5be8)

### 6.1 轮询

轮询是upstream的默认分配方式，即每个请求按照时间顺序轮流分配到不同的后端服务器，如果某个后端服务器 down 掉后，能自动剔除。

```shell
# 轮询
upstream zhang21（名称）{
    server 192.168.1.11：8888；
    server 192.168.1.22：8888；
    server 192.168.1.33：8888；
}
```

### 6.2 权重

轮询的加强版，既可以指定轮询比率，weight 和访问几率成正比，主要应用于后端服务器异质的场景下。

```shell
upstream zhang21 {
    server 192.168.1.11 weight=1；
    server 192.168.1.22 weight=2；
    server 192.168.1.33 weight=3；

}
```

### 6.3 ipHash

每个请求按照访问 Ip（即Nginx的前置服务器或客户端IP）的 hash结果分配，这样每个访客会固定访问一个后端服务器，可以解决 session 一致问题。

```shell
upstream zhang21 {
    ip_hash;
    server 192.168.1.11:7777;
    server 192.168.1.22:8888;
    server 192.168.1.33:9999;
}
```

### 6.4 fair

fair顾名思义，公平地按照后端服务器的响应时间（rt）来分配请求，响应时间（rt）小的后端服务器优先分配请求。

```shell
upstream zhang21 {



}
```

### 6.5  url_hash

与 ip_hash类似，但是按照访问 url 的 hash 结果来分配请求，使得每个 url 定向到同一个后端服务器，主要应用于后端服务器为缓存的场景下。

```shell
upstream zhang21 {
    server 192.168.1.11;
    server 192.168.1.22;
    server 192.168.1.33;
    hash $request_uri；
    hash_method crc32；

}
```

## 7. linux上的问题

```shell
nginx: [emerg] bind() to 0.0.0.0:8089 failed (13: Permission denied)

yum provides semanage
yum -y install policycoreutils-python.x86_64
semanage port -a -t http_port_t -p tcp 8090 
```

* 增加端口权限
  
  ```shell
  semanage port -a -t http_port_t -p tcp 8090
  ```

* 查看端口权限
  
  ```shell
  semanage port -l | grep http_port_t
  ```

* 查看linux 是否运行
  
  ```shell
  ps -A | grep nginx
  ```

* 查看端口占用
  
  ```shell
  netstat -tunlp | grep 端口号
  ```

* 查看防火墙
  
  ```shell
  firewall-cmd --list-ports
  ```
