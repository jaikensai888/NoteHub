# hbase

## 1.安装

TODO：

## 2.使用

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