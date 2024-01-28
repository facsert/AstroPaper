---
author: facsert
pubDatetime: 2024-01-22 10:59:43
title: Python mongo
slug: Python mongo
featured: false
draft: false
tags:
  - Python
description: "Python mongo"
---

[Pymongo](https://pymongo.readthedocs.io/en/stable/)文档

## Table of Contents

## 安装

```bash
 $ python -m pip install pymongo
 $ pip list | grep pymongo
 > pymongo              4.6.1
```

## 数据库和集合

```py
from mongo import mongoClient

mongo = MongoClient("mongodb://localhost:27017") # 连接 mongo
dbs = mongo.list_database_names()                # 列出所有数据库

db = mongo['db']                                 # 按名称创建/获取数据库(数据库集合中创建元素后才实际创建数据库)
mongo.drop_database('db')                        # 删除数据库


collection = db['student']                       # 按名称创建/获取集合, 类似关系数据库中的表
db.drop_collection('student')                    # 删除集合

collection.rename('person')                      # 重命名集合
```

集合约束

ASCENDING: 升序
DESCENDING: 降序

```py
collection.create_index([("index", ASCENDING)])  # 集合按升序索引
collection.create_index("index", unique=True)    # 集合按升序索引(默认值), 且 index 唯一

```

## Add

```py

item = collection.insert_one(dict)               # 查询单个数据
item.inserted_id                                 # 插入数据的 id

collection.insert_one({'id':1, 'name': 'John'})

item = collection.insert_many(iterable[dict])    # 查询多个数据
item.inserted_ids                                # 打印多个插入数据的 id


collection.insert_many([
    id: i, value: v
    for i in enumerate('abcd')
])
```

## Query

pymongo 通过键值对匹配查询, 也可以通过一些特殊字段描述条件

`$eq`: 值等于, {'name': 'John'} name 为 Jhon 的数据
`$ne`: 值不等于, {'name': {'$ne': 'John'}} name 不等于 John 的值
`$lt, $lte`: 小于, 小于等于 {'num': {'$lt': 10}} num 小于 10 的数据
`$gt, $gte`: 大于, 大于等于 {'num': {'$gte': 10}}  num 大于等于 10 的数据
`$in`: 在范围内 {'index': {'$in': [1, 2, 3]}} index 的值是 1, 2, 3 的数据
`$regex`: 符合正则表达式 {'name': {'$regex': '^J'}} name 符合正则的数据

逻辑与: {'num': {'$gt':10, '$lt': 20}} num 大于 10 且小于 20 的数据
逻辑或: {'$or': [{'name': 'John'}, {'index': 3}]} name 为 John 或 index 为 3 的数据

```py
iten = collection.find_one(dict)                   # 查询单个数据 -> dict

items = collection.find(dict)                      # 查询多个数据 -> generation(仅能遍历一次)
```

## Update

`$set` : 更新字段  `{'index': 3}, {'$set': {'name': 'John'}}` index 为 3 的数据, name 字段改为 John  
`$inc` : 字段值自增(可负数) `{'index': 3}, {'$inc': {'n': 1}}` index 3 的数据, n 字段加 1  
`$unset`: 删除字段  
`$rename`: 字段重命名  
`$push`: 数组结尾添加值  
`$pop`: 数组弹出一个元素, 1 表示末尾, -1 表示开头弹出  
`$currentDate`: 更新字段为当前日期  

```py

collection.replace_one(dict, dict)               # 数据替换

item = collection.update_one(dict, dict)         # 更新指定单个数据的字段
item.matched_count                               # 匹配数量
item.modified_count                              # 修改数量

items = collection.update_many(dict, dict)       # 修改指定的所有数据字段
```

## Delete

```py
item = collection.delete_one(dict)               # 删除匹配的单个数据
item.deleted_count                               # 删除数量

collection.delete_many(dict)                     # 删除匹配的所有数据
```