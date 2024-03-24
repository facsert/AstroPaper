---
author: facsert
pubDatetime: 2023-04-14 15:28:43
title: Mongo Configuration
slug: Mongo Configuration
featured: false
draft: false
tags:
  - Mongo
description: "Mongo 基本配置"
---

<!--
 * @Author       : facsert
 * @Date         : 2023-04-14 15:28:43
 * @LastEditTime : 2023-07-28 11:46:45
 * @Description  : edit description
-->

## Table of Contents

## Mongo 安装

通过 docker 运行 mongo 数据库

```bash
 $ docker pull mongo
 $ docker images
 > REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
 > mongo        latest    32c5c1d795d9   2 weeks ago   644MB

 $ docker run -d --restart=always -p 27017:27017 --name mongodb -v /root/Desktop/Mongo:/data/db  mongo
 $ docker ps
 > CONTAINER ID   IMAGE  COMMAND                 CREATED        STATUS         PORTS                                           NAMES
 > b4da33977293   mongo  "docker-entrypoint.s…"  6 seconds ago  Up 5 seconds   0.0.0.0:27017->27017/tcp, :::27017->27017/tcp   mongodb
```

- 以 mongo 为镜像, 创建容器 mongodb
- 容器内 /data/db 目录挂载到系统 /root/Desktop/Mongo 目录下
- 系统端口 27017 与容器内 27017 端口映射

```bash
 $ docker exec -it mongodb bash

 $ mongosh                                       # 进入 mongo shell, 早期版本使用 mongo 命令
 >
```

[Mongo Shell](https://mongodb.net.cn/manual/reference/method/)

## 数据库

Mongo 中可以创建多个数据库, 数据库可以创建多个集合(类似于表), 集合用于存储数据

```js
// 显示所有数据库
show dbs
admin    40.00 KiB
config  108.00 KiB
local    40.00 KiB

// 创建或切换数据库(数据库有数据才会创建, show 命令不显示)
use person
switched to db redis

// 显示当前数据库名称
db
person

// 数据库 test 的信息
db.stats()
{
  db: 'person',
  collections: Long('0'),
  views: Long('0'),
  objects: Long('0'),
  avgObjSize: 0,
  dataSize: 0,
  storageSize: 0,
  indexes: Long('0'),
  indexSize: 0,
  totalSize: 0,
  scaleFactor: Long('1'),
  fsUsedSize: 0,
  fsTotalSize: 0,
  ok: 1
}

// 删除数据库 person
db.dropDatabase()
{ ok: 1, dropped: 'person' }
```

## 集合

```js
// 查看数据库内的所有集合
show collections
{ ok: 1 }

// 创建集合, 并配置(集合有数据才会创建)
db.createCollection(name, option)
db.createCollection("studens", { capped : true, size : 5242880, max : 5000 })

// 查看 studens 集合状态
db.studens.stats()

// 删除 studens 集合
db.studens.drop()

// 集合的数据数量
db.students.countDocuments()
```

```js
// 查看集合所有索引(排序方式, 字段约束)
db.students.getIndexes()
[
  { v: 2, key: { _id: 1 }, name: '_id_' },
]

// 添加 name 字段升序(1 升序, -1 降序), 且 name 字段值唯一约束(unique)
db.students.createIndex({name: 1}, {unique: true})
db.students.getIndexes()
[
  { v: 2, key: { _id: 1 }, name: '_id_' },
  { v: 2, key: { name: 1 }, name: 'name_1', unique: true }
]
```

|  option  |  type   | description                                                                                             |
| :------: | :-----: | :------------------------------------------------------------------------------------------------------ |
| `capped` | Boolean | true: 集合启用 size 限制, 超过部分自动覆盖最早的条目。启动该选项同时也需指定 size 数值                  |
|  `size`  | number  | 设定集合最大可使用字节数。capped 为 true 时设置                                                         |
|  `max`   | number  | 设置集合能容纳的最大文档数量。Size限制优先于数量限制。文档数量未到 max, size 到达限制时也会触发文档覆盖 |

## 插入数据

- insertOne
- insertMany

```js
// 插入单个数据
db.student.insertOne({name: "John", age: 19})
{
  acknowledged: true,
  insertedId: ObjectId('65d1fe9776bc4fe476eabd1a')
}

// 插入多个数据
db.coll.insertMany([
  { name: "lily"},
  { name: "Alen", hobby: "swim"}
])
```

## 查询数据

- findOne
- find

```js
// 查询匹配的第一个数据(返回匹配的第一个数据)
db.students.findOne({name: 'lily'})
{ _id: ObjectId('65d200a776bc4fe476eabd1e'), name: 'lily' }

// 查询所有匹配的数据
db.students.find({})
[
  { _id: ObjectId('65d1ff0e76bc4fe476eabd1b'), name: 'John', age: 19 },
  { _id: ObjectId('65d200a776bc4fe476eabd1e'), name: 'lily' },
  {
    _id: ObjectId('65d200a776bc4fe476eabd1f'),
    name: 'Alen',
    hobby: 'swim'
  }
]

// 数据范围查询($gt $gte $lt $lte $ne $in)
db.students.find({age: {$gt: 15}})
[
  { _id: ObjectId('65d1ff0e76bc4fe476eabd1b'), name: 'John', age: 19 }
]

// 属性存在查询
db.students.findOne({hobby: {$exists: true}})
{
  _id: ObjectId('65d200a776bc4fe476eabd1f'),
  name: 'Alen',
  hobby: 'swim'
}

// 值的正则匹配查询
db.students.find({name: {$regex:'lily|John'}})
[
  { _id: ObjectId('65d1ff0e76bc4fe476eabd1b'), name: 'John', age: 19 },
  { _id: ObjectId('65d200a776bc4fe476eabd1e'), name: 'lily' }
]

// 逻辑或查询
db.students.find({$or: [{name: 'lily'}, {name: 'Alen'}]})
[
  { _id: ObjectId('65d200a776bc4fe476eabd1e'), name: 'lily' },
  {
    _id: ObjectId('65d200a776bc4fe476eabd1f'),
    name: 'Alen',
    hobby: 'swim'
  }
]
```

## 更新数据

- updateOne
- updateMany

`db.coll.updateMany(filter, set)`

```js
// 更新单个数据
db.students.updateOne({name: 'lily'}, {$set: {"age": 18} })
db.students.findOne({name: 'lily'})
{ _id: ObjectId('65d200a776bc4fe476eabd1e'), name: 'lily', age: 18 }

// 更新多个数据
db.students.updateMany({name: {$exists: true}}, {$set: {"description": "school student"} })
db.students.find({})
[
  {
    _id: ObjectId('65d1ff0e76bc4fe476eabd1b'),
    name: 'John',
    age: 19,
    description: 'school student'
  },
  {
    _id: ObjectId('65d200a776bc4fe476eabd1e'),
    name: 'lily',
    age: 18,
    description: 'school student'
  },
  {
    _id: ObjectId('65d200a776bc4fe476eabd1f'),
    name: 'Alen',
    hobby: 'swim',
    description: 'school student'
  }
]

// 重命名字段
db.students.updateOne({name: 'lily'}, {$rename: {"description": "desc"} })
db.students.findOne({name: 'lily'})
{
  _id: ObjectId('65d200a776bc4fe476eabd1e'),
  name: 'lily',
  age: 18,
  desc: 'school student'
}

// 删除字段
db.students.updateOne({name: 'Alen'}, {$unset: {"hobby": ""} })
db.students.findOne({name: 'Alen'})
{
  _id: ObjectId('65d200a776bc4fe476eabd1f'),
  name: 'Alen',
  description: 'school student Alen'
}
```

## 删除数据

- deleteOne
- deleteMany

`db.coll.deleteMany(filter)`

```js
// 删除单个数据
db.students.deleteOne({name: "Alen"})
db.students.find({})
[
  {
    _id: ObjectId('65d1ff0e76bc4fe476eabd1b'),
    name: 'John',
    age: 19,
    description: 'school student'
  },
  {
    _id: ObjectId('65d200a776bc4fe476eabd1e'),
    name: 'lily',
    age: 18,
    desc: 'school student'
  }
]

// 删除多个数据
db.students.deleteMany({})
{ acknowledged: true, deletedCount: 2 }
db.students.find({})
```

## 备份恢复

- mongodump
- mongorestore

```bash
# 备份/恢复 mongo 全部数据到 /data/db/mongo_backup 路径下(映射路径, 方便取出)
$ mongodump --out  /data/db/mongo_backup
$ mongorestore  /data/db/mongo_backup

# 备份和恢复指定数据库到指定路径
$ mongodump --db database_name --out path
$ mongorestore --db database_name --out path

# 备份和恢复特定集合到指定路径
$ mongodump --db database_name --collection coll_name --out path
$ mongorestore --db database_name --collection coll_name  --out path
```
