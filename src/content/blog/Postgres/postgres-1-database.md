---
author: facsert
pubDatetime: 2024-02-22 20:31:18
title: Postgresql database
slug: Postgresql database
featured: false
draft: false
tags:
  - Postgresql
description: "Postgresql 数据库"
---

## Table of Contents

## 数据库

```sql
/* 创建数据库 */
CREATE DATABASE database_name WITH
  OWNER             = role_name                  /* 数据库所有者 */
  TEMPLATE          = template                   /* 以模板创建(默认为 template1) */
  ENCODING          = encoding                   /* 编码格式(默认 UTF8) */
  LC_COLLATE        = collate                    /* 数据库使用的排序规则 */
  LC_CTYPE          = ctype                      /* 数据库字符分类 */
  TABLESPACE        = tablespace_name            /* 表空间 */
  ALLOW_CONNECTIONS = true | false               /* 是否允许连接 */
  CONNECTION LIMIT  = max_concurrent_connection  /* 最大连接数(默认 -1, 表示无限制) */
  IS_TEMPLATE       = true | false ;             /* 是否为模板数据库 */


/* 创建数据库示例 */
CREATE DATABASE db WITH
  OWNER = postgres
  ENCODING = 'UTF8'
  CONNECTION LIMIT = 100;

```

```sql
/* 创建数据库 */
 CREATE DATABASE person;
 > CREATE DATABASE

 \l
                                                  List of databases
 Name   |  Owner   | Encoding | Locale Provider |  Collate   |   Ctype    | ICU Locale | ICU Rules | Access privileges
--------+----------+----------+-----------------+------------+------------+------------+-----------+------------------
 person | postgres | UTF8     | libc            | en_US.utf8 | en_US.utf8 |            |           |
 ......
 ......

 /* 连接指定数据库 */
 \c people
 > You are now connected to database "people" as user "postgres".

 /* 修改数据库名称(不能修改当前连接的数据库名) */
 ALTER DATABASE person RENAME TO people;
 > ALTER DATABASE

 \l
                                                  List of databases
 Name   |  Owner   | Encoding | Locale Provider |  Collate   |   Ctype    | ICU Locale | ICU Rules | Access privileges
 -------+----------+----------+-----------------+------------+------------+------------+-----------+-------------------
 people | postgres | UTF8     | libc            | en_US.utf8 | en_US.utf8 |            |           |


/* 删除指定数据库(不能删除当前连接的数据库名) */
 DROP DATABASE IF EXISTS people;
 > DROP DATABASE
```
