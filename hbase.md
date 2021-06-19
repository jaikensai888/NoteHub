# hbase

[top]

## 参考资料

* hbase默认端口

```txt
16000:hbaseMaster
16010:hbaseUi
16020:hbaseRegionServer
16030:hbaseRegionServerUi
2181:zooKeeper
```

* 参考案例

 > https://zhuanlan.zhihu.com/p/166228818 成功案例

## 安装

* 版本兼容性问题(注意版本)

>http://hbase.apache.org/book.html#java

hbase与jdk

![jdk](/img/hbaseJava.png)

hbase与hadoop

![jdk](/img/hbaseHadoop.png)

* Linux 安装(单机版)

1. 前提JavaSdk已经安装
2. 安装Hbase  [点击教程](http://hbase.org.cn/docs/32.html)

* windows 安装(单机版)(具麻烦) 请注意版本

1. 先安装Hadoop  [点击教程]( https://blog.csdn.net/weixin_43986204/article/details/90210010)

2. 安装Hbase  [点击教程](http://hbase.org.cn/docs/32.html)

3. 运行Hbase后 可以登录localHost:16030 查看相关信息

## 使用

### hbase shell

* 查看状态

```shell
status
```

* 查看版本

```shell
version
```

* 查看数据表

```shell
list   显示所有table
```

* 创建表

```shell
create 'tableName','colName1','colName2','colName2:colName2_1'
```

* 插入记录

```shell
put 'tableName','rowKey','colName1','value'

put 'tableName','rowKey','colName:colName2_1','value'
```

* 查询表

```shell
count 'tableName'

get 'tableName','rowKey'

get 'tableName','rowKey','colName'

```

* 扫描筛选表

```shell

scan 'tableName'  --全表扫描

scan 'tableName',{LIMIT=>1} --查询1条数据

```

## 问题
Class path contains multiple SLF4J bindings

The authenticity of host '127.0.0.1 (127.0.0.1)' can't be established.

127.0.0.1: Authentication failed