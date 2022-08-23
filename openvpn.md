# OpenSvn配置教程

## 安装

----

* 环境
  
    安装版本Openvpen 2.3.10-1601
  
    操作系统Windows
  
    服务器IP：192.168.88.123  VPN：192.168.89.1
  
    客户端IP：192.168.78.3
1. 安装下面两个选项默认不勾选，我们需要**勾选制作证书工具**
   
    ![image](./Img/openVpn/1.png)

2. 安装完成后,多了一个Tap的本地链接
   
    ![set1](./Img/openVpn/2.png)

3. 要想客户端能够通过服务端上网需要调整服务端本地网卡共享
   
    ![set1](./Img/openVpn/3.png)

4. 修改注册表

HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters 将IPEnableRouter改为1

5. 服务端防护墙设置，关闭防火墙或将8080端口加入到防火墙允许通过的端口中去

## 生成证书（如果已经有可以忽略）

1. 初始化证书生成脚本 openvpn/easy-rsa/vars.bat.sample
   
   ```shell
   set KEY_COUNTRY=CN 国家 
   set KEY_PROVINCE=GuanDong 省分 
   set KEY_CITY=GuanZhou 城市 
   set KEY_ORG=OV980 组织名 
   set KEY_EMAIL=admin@itca.cc 邮箱
   ```

2. 执行初始化命令
   
   ```shell
    cd C:\Program Files\OpenVPN\easy-rsa
    init-config  # init-config 会把 vars.bat.sample 复制为 var.bat
    vars # vars.bat 用来设置一些变量，主要就是配置文件中修改的那部分
    clean-all  # 会清掉 C: \Program File\OpenVPN\easy-rsa\keys 目录，再把 index.txt 和 serial 文件放进来
   ```
   
    ![RESULT](./Img/openVpn/4.png)

3. 生成证书
   
   ```shell
   build-ca # 生成根证书
   ```
   
    回车会直接填写默认参数

4. 生成 dh2048.pem 文件
   
   ```shell
   build-dh # 生成 dh1024.pem 文件，Server 使用 TLS(OpenSSL) 必须要有的文件
   ```

5. 生成服务端密钥
   
   ```shell
   build-key-server server # 生成服务端密钥和证书
   # Common Name 必须填，且不能与执行 build-dh 时相同，否则会报failed to update database TXT_DB error number 2
   ```

6. 生成客户端秘钥
   
   ```shell
   build-key client  # 客户端生成密钥和证书
   # Common Name 必须填，且不能与执行 build-key-server 时相同，否则会报failed to update database TXT_DB error number 2
   ```

7. 生成生成ta.key文件 可选：防止DODS攻击
   
   ```shell
   openvpn --genkey --secret keys/ta.key
   ```

8. 执行后结果
   
    生成openvpn/easy-rsa/keys
   
    ![result](./Img/openVpn/5.png)

## 配置Openvpn服务端

1. 将keys目录ca.crt、ca.key、server01.crt、server01.csr、server01.key、dh2048.pem、ta.key文件拷贝到 openvpn/config 

2. 创建server.ovpn文件
   
   ```server.open
   port 8080
   proto tcp 
   dev tun
   ca ca.crt
   cert server.crt
   key server.key # This file should be kept secret
   ;crl-verify vpncrl.pem
   dh dh1024.pem
   server 192.168.89.0 255.255.255.0
   ifconfig-pool-persist ipp.txt
   push "redirect-gateway def1 bypass-dhcp" 
   push "dhcp-option DNS 218.85.157.99" 
   push "dhcp-option DNS 223.5.5.5" 
   push "route 192.168.88.0 255.255.255.0"
   client-to-client
   ;duplicate-cn
   keepalive 10 120
   tls-auth ta.key 0 # This file is secret
   comp-lzo
   ;max-clients 100
   user nobody
   group nobody
   persist-key
   persist-tun
   status openvpn-status.log
   verb 4
   ```

## 配置Openvpn 客户端

1. 将client01.crt、client01.csr、client01.key、ca.key、ca.crt、ta.key 复制到客户端的config

2. 在config创建client.ovpn文件
   
   ```ovpn
   client
   dev tun
   proto tcp
   remote IP 8080
   resolv-retry infinite
   nobind
   user nobody
   group nobody
   ;route 192.168.0.0 255.255.252.0
   persist-key
   persist-tun
   ;http-proxy-retry # retry on connection failures
   ;http-proxy [proxy server] [proxy port #]
   ca ca.crt
   cert client.crt
   key client.key
   ns-cert-type server
   tls-auth ta.key 1
   comp-lzo
   # Set log file verbosity.
   verb 4
   ```

## 参考文档

* [Easy-rasa官方](https://github.com/OpenVPN/easy-rsa/releases)
* [OpenVPN官方](https://openvpn.net/community-downloads/)
* [OpenVPN Windows 平台安装部署教程](http://www.fyluo.com/m/?post=198)
* [在Windows Server2019上搭建OpenVPN服务端](https://blog.csdn.net/zxm8513/article/details/117340147)