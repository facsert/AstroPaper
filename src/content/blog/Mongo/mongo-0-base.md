---
author: facsert
pubDatetime: 2023-04-14 15:28:43
title: Mongo Configuration
postSlug: ""
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

## 示例

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

## mongo 容器

```bash
 $ docker exec -it mongodb bash

 $ mongosh                                       # 进入 mongo shell, 早期版本使用 mongo 命令
 >
```

## mongo

数据库
集合
文档

```bash
test                                             # 数据库名称
    student                                      # 集合名称
        <id1>: {'_id': <id1>, 'name': 'John'}    # 文档，类似行, 一个文档包含一个 json 数据
        <id2>: {'_id': <id2>, 'name': 'peter'}
    teacher                                      # 集合名称
        <id1>: {'_id': <id1>, 'name': 'John'}    # 文档，类似行, 一个文档包含一个 json 数据
        <id2>: {'_id': <id2>, 'name': 'peter'}

```

## 数据库

```js
 > show dbs                                      // 显示所有数据库
 admin    40.00 KiB
 config  108.00 KiB
 local    40.00 KiB
 test     40.00 KiB

 > use redis                                      // 创建或切换数据库
 switched to db redis

 > db                                            // 显示当前数据库名称
 redis

 > db.stats()                                    // 数据库 test 的信息
 {
  db: 'redis',
  collections: 0,
  views: 0,
  objects: 0,
  avgObjSize: 0,
  dataSize: 0,
  storageSize: 0,
  indexes: 0,
  indexSize: 0,
  totalSize: 0,
  scaleFactor: 1,
  fsUsedSize: 0,
  fsTotalSize: 0,
  ok: 1
 }

 > db.dropDatabase()                             // 删除数据库 redis
 { ok: 1, dropped: 'redis' }
```

## 集合

```js
 > show collections                              // 查看数据库内的所有集合

 > db.createCollection(<name>, <option>)         // 创建集合, 并配置
 > db.createCollection("log", { capped : true, size : 5242880, max : 5000 } )

 > db.log.drop()                                 // 删除 log 集合
```

|  option  |  type   | description                                                                                             |
| :------: | :-----: | :------------------------------------------------------------------------------------------------------ |
| `capped` | Boolean | true: 集合启用 size 限制, 超过部分自动覆盖最早的条目。启动该选项同时也需指定 size 数值                  |
|  `size`  | number  | 设定集合最大可使用字节数。capped 为 true 时设置                                                         |
|  `max`   | number  | 设置集合能容纳的最大文档数量。Size限制优先于数量限制。文档数量未到 max, size 到达限制时也会触发文档覆盖 |

## 文档

```js
 > db.<collection>.insert(<json>)                // 指定集合插入 json 数据
 > db.<collection>.insert([<json>, <json>])      // 单次插入多个 json 数据, 与多次插入单个 json 一致

```
