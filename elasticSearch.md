# ElasticSearch

## 基础概念

* 端口

9200

* 查看当前节点的所有Index

http://localhost:9200/_cat/indices?v

* 查看Index所包含的Type

localhost:9200/_mapping?pretty=true'

* 数据单元
Index>Document

Index（即数据库）

Document(记录)

## 使用

matchQuery：会将搜索词分词，再与目标查询字段进行匹配，若分词中的任意一个词与目标字段匹配上，则可查询到。

termQuery：不会对搜索词进行分词处理，而是作为一个整体与目标字段进行匹配，若完全匹配，则可查询到。

