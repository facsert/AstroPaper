---
author: facsert
pubDatetime: 2023-12-06 22:05:18
title: Postgresql configuration
slug: Postgresql configuration
featured: false
draft: false
tags:
  - Postgresql
description: "Postgresql 配置"
---

<!--
 * @Author: facsert
 * @Date: 2023-12-06 22:05:18
 * @LastEditTime: 2023-12-06 22:19:33
 * @LastEditors: facsert
 * @Description:
-->

## Table of Contents

## 安装

```bash
 # 下载 postgres 镜像
 $ docker pull postgres
 $ docker images
 > REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
 > postgres     latest    75b7bff7c3ad   6 weeks ago   425MB

 # 使用镜像创建容器, 端口映射, 容器名称, 环境变量(密码), 路径映射
 $ docker run \
     -p 5432:5432 \
     --name postgres \
     -e POSTGRES_PASSWORD=password \
     -v /root/Desktop/Postgres:/var/lib/postgresql/data \
     -d postgres

 # 进入容器
 $ docker exec -it postgres bash

 # 使用 postgres 用户连接数据库
 $ psql -U postgres

 # 查看数据库列表
 \l
                                                      List of databases
   Name    |  Owner   | Encoding | Locale Provider |  Collate   |   Ctype    | ICU Locale | ICU Rules |   Access privileges
-----------+----------+----------+-----------------+------------+------------+------------+-----------+-----------------------
 postgres  | postgres | UTF8     | libc            | en_US.utf8 | en_US.utf8 |            |           |
 template0 | postgres | UTF8     | libc            | en_US.utf8 | en_US.utf8 |            |           | =c/postgres          +
           |          |          |                 |            |            |            |           | postgres=CTc/postgres
 template1 | postgres | UTF8     | libc            | en_US.utf8 | en_US.utf8 |            |           | =c/postgres          +
           |          |          |                 |            |            |            |           | postgres=CTc/postgres
(3 rows)
```

PostgreSQL 命令行在处理 SQL 语句时, 不区分大小写, 结尾以分号(;)结尾  
`psql -h host -p port -U username -d databasename -W password`

```sql
\?                                               /* 所有命令帮助 */
\l                                               /* 列出所有数据库 */
\d                                               /* 列出数据库中所有表 */
\dt                                              /* 列出数据库中所有表 */
\d [table_name]                                  /* 显示指定表的结构 */
\di                                              /* 列出数据库中所有 index */
\dv                                              /* 列出数据库中所有 view */
\h                                               /* sql命令帮助 */
\q                                               /* 退出连接 */
\c [database_name]                               /* 切换到指定的数据库 */
\c                                               /* 显示当前数据库名称和用户 */
\conninfo                                        /* 显示客户端的连接信息 */
\du                                              /* 显示所有用户 */
\dn                                              /* 显示数据库中的schema */
\encoding                                        /* 显示字符集 */
select version();                                /* 显示版本信息 */
\i testdb.sql                                    /* 执行sql文件 */
\x                                               /* 扩展展示结果信息，相当于MySQL的\G */
\o /tmp/test.txt                                 /* 将下一条sql执行结果导入文件中 */
```
