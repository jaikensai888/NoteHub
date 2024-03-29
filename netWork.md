# netWork

---

> 网络知识学习

## 1.网络地址的构成

### 1.1 IP地址

* 表达
由4个8个bit 构成工32位bit 用于表达当前局域网地址;
0.0.0.0=>00000000.00000000.0000000.00000000

* **运算**
Ip地址分别有网络号与主机号两部分组成  

1. 网络号 （相同网络号的主机才算用一个局域网）**子掩码与运算**
2. 主机号 **非子掩码与运算**

    ```text
    # 计算例子
    IP:192.168.0.103
    子网掩码:255.255.255.0
        
    // 计算网络号
    // 1.Ip 转换成32位Bit 11000000 10101000 00000000 00000011
    // 2.子网掩码 转换成32位Bit 11111111.11111111.11111111.00000000
    // 3. 进行[与]运算得出子网络号
    11000000 10101000 00000000 00000011
    11111111.11111111.11111111.00000000
    11000000 10101000 00000000 00000000 =》192.160.0.0 （网络号/网段）

    // 计算主机号
    // 1.Ip 转换成32位Bit 11000000 10101000 00000000 00000011
    // 2.子网掩码 转换成32位Bit 11111111.11111111.11111111.00000000
    // 3.子网掩码 进行非运算 00000000.00000000.00000000.11111111
    // 4. Ip和子网掩码（非）进行[与]运算得出子主机号
    11000000 10101000 00000000 00000011
    00000000.00000000.00000000.11111111
    00000000.00000000.00000000.00000011 =>0.0.0.3
    ```


### 1.2子网掩码（SUBNet Mask）

与IP结合用于区分网段号与主机号
  
### 1.3 网关

门跳转路由所在

1. 寻址无果的时候的目的地
2. 网段跳转的门；

### 1.4 广播IP

## 2 相关使用与配置

* route命令
  
```shell
# windows
# 查看本机路由表
router print
# 增加本地路由
router add
```

* host文件
用于绑定本地dns与ip地址（分布式集群经常使用）

```shell
# windows路径
/Windows/System32/Drivers/etc/hosts
# linux路径
/etc/hosts

# 基本用户
localHost 127.0.0.1
```

  

## 3 实际案例参考

* [不同网段之间访问案例](https://www.cnblogs.com/embedded-linux/p/10200831.html)