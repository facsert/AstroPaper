---
author: facsert
pubDatetime: 2024-02-22 20:31:28
title: Postgresql table
slug: Postgresql table
featured: false
draft: false
tags:
  - Postgresql
description: "Postgresql 表操作"
---

## Table of Contents

## 数据类型

| 数据类型 | 代码                    | 描述                                                                            |
| :------: | :---------------------- | :------------------------------------------------------------------------------ |
|  布尔型  | BOOLEAN                 | TRUE('true', 't', 'yes', 'y', '1'), FALSE('false', 'f', 'no', 'n', '0') 或 NULL |
| 字符类型 | CHAR(50)                | 固定长度的字符类型, 长度不足, 空格补齐, 超出将异常(不推荐)                      |
| 字符类型 | VARCHAR(50)             | 可变长度的字符类型，不补长度, 超出将异常(不带长度与 TEXT 相同)                  |
| 字符类型 | TEXT                    | 变长度的字符类型, 存储任意长度的字符串                                          |
| 整数类型 | SMALLINT/INT2           | 2字节有符号整数(-32768, 32767)                                                  |
| 整数类型 | INTEGER/INT/INT4        | 4字节有符号整数(-2147483648, 2147483647)                                        |
| 整数类型 | BIGINT/INT8             | 8字节有符号整数(-9223372036854775808, 9223372036854775807)                      |
| 浮点类型 | REAL/FLOAT4             | 单精度浮点数(4字节)                                                             |
| 浮点类型 | DOUBLE PRECISION/FLOAT8 | 双精度浮点数(8字节)                                                             |
| 浮点类型 | NUMERIC(p, s)           | 高精度数字, p表示数字总长度, s表示小数位数                                      |
| 时间类型 | DATE                    | 日期(yyyy-mm-dd)                                                                |
| 时间类型 | TIME                    | 一天中的某个时刻(HH:MM:SS.ssssss)                                               |
| 时间类型 | TIMESTAMP               | 时间日期和时刻(YYYY-MM-DD HH:MI:SS[.ssssss])                                    |
| 时间类型 | INTERVAL                | 时间间隔                                                                        |
| 时间类型 | TIMESTAMP               | 带时区的时间日期和时刻                                                          |
| JSON类型 | JSON                    | JSON格式的字符串                                                                |
| 数组类型 | ARRAY                   | 数组                                                                            |
| UUID类型 | UUID                    | UUID                                                                            |

## 创建表

```sql
/* 创建表 */
CREATE TABLE [IF NOT EXISTS] table_name (
   column1 datatype(length) column_contraint,
   column2 datatype(length) column_contraint,
   column3 datatype(length) column_contraint,
   table_constraints
);

/* 创建表示例(create 是关键字不可作为表头) */
CREATE TABLE IF NOT EXISTS students (            /* IF NOT EXISTS 表存在则不创建 */
   id SERIAL NOT NULL PRIMARY KEY,               /* id 作为主键且不能为空 */
   name VARCHAR(20) NOT NULL,                    /* name 最长为20字符且不能为空 */
   age  INT,                                     /* age 可以为空 */
   locked BOOLEAN NOT NULL DEFAULT false,        /* locked 默认为 false 且不能为空 */
   create_at TIMESTAMP NOT NULL                  /* create_at 时间戳且不能为空 */
);

/* 查看所有表 */
\d
          List of relations
 Schema |   Name   | Type  |  Owner
--------+----------+-------+----------
 public | students | table | postgres

/* 查看指定的表详细信息 */
\d students
                         Table "public.students"
  Column   |            Type             | Collation | Nullable | Default
-----------+-----------------------------+-----------+----------+---------
 id        | integer                     |           | not null |
 name      | character varying(20)       |           | not null |
 age       | integer                     |           |          |
 locked    | boolean                     |           | not null | false
 create_at | timestamp without time zone |           | not null |
Indexes:
    "students_pkey" PRIMARY KEY, btree (id)

/* 删除表 */
DROP TABLE IF EXISTS students;
```

## 修改表

```sql
ALTER
```

## 表约束

### 主键

使用 `PRIMARY KEY` 为表创建主键

- 一张表只能有一个主键, 表可以没有主键
- 主键必须唯一, 可以由一个列或多个列共同组成
- 主键列不允许为 `NULL`

```sql
/* 创建表, id 为主键 */
CREATE TABLE users (
  id INTEGER PRIMARY KEY,
  name VARCHAR(45),
);

/* 无主键的表, 添加主键 */
ALTER TABLE users ADD PRIMARY KEY (id);

/* 查看表结构 */
\d users
                      Table "public.users"
Column |         Type          | Collation | Nullable | Default
--------+-----------------------+-----------+----------+---------
id     | integer               |           | not null |
name   | character varying(45) |           |          |
Indexes:
    "users_pkey" PRIMARY KEY, btree (id)

/* 删除主键 */
TABLE TABLE users DROP CONSTRAINT users_pkey;
```

设置主键为 SERIAL, UUID 自动生成主键

```sql
/* SERIAL 列自动生成连续递增整数 */
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  name VARCHAR(45)
);

/* 使用 UUID 作为主键 */
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name VARCHAR(45)
);

/* 多列组合主键 */
CREATE TABLE users (
  id SERIAL,
  name VARCHAR(45),
  PRIMARY KEY (id, name)
);
```

## NOT NULL

使用 `NOT NULL` 使列的值不能为空

```sql
/* id 列不能为空 */
CREATE TABLE users (
    id INT NOT NULL
);

/* 列添加 NOT NULL 约束(需将列所有值设为非空才能添加) */
ALTER TABLE users ALTER COLUMN id SET NOT NULL;

/* 列删除 NOT NULL 约束 */
ALTER TABLE users ALTER COLUMN id DROP NOT NULL;
```

## UNIQUE

使用 `UNIQUE` 使列的值唯一

```sql
/* id 列所有值均唯一 */
CREATE TABLE users (
    id INT UNIQUE
);

/* 添加多列唯一约束 */
CREATE TABLE users (
    id INT,
    name VARCHAR(20),
    CONSTRAINT unique_cols
      UNIQUE (id, name)
);

/* 列添加 UNIQUE 约束(需将列所有值设为非空才能添加) */
ALTER TABLE users ADD CONSTRAINT unique_cols UNIQUE (id);

/* 列删除 UNIQUE 约束 */
ALTER TABLE users DROP CONSTRAINT unique_cols;
```

## CHECK

使用 `CHECK` 约束限制列的值范围, 不符合的数据无法添加

```sql
/* age 列添加 CHECK 约束 */
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    age INTEGER NOT NULL,
    CONSTAINT user_age
      CHECK(age > 0)
);

/* 添加 CHECK 约束 */
ALTER TABLE users ADD CONSTRAINT user_age CHECK (age > 0);

/* 删除 CHECK 约束 */
ALTER TABLE users DROP CONSTRAINT user_age;
```
