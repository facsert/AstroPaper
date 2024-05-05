---
author: facsert
pubDatetime: 2024-02-22 20:31:38
title: Postgresql data
slug: Postgresql data
featured: false
draft: false
tags:
  - Postgresql
description: "Postgresql 数据操作"
---

## Table of Contents

```sql
/* 创建一个表作为示例 */
CREATE TABLE IF NOT EXISTS students (
   id SERIAL NOT NULL PRIMARY KEY,
   name VARCHAR(20) NOT NULL,
   age  INT,
   locked BOOLEAN NOT NULL DEFAULT false,
   create_at TIMESTAMP NOT NULL
);

\d students
                                        Table "public.students"
  Column   |            Type             | Collation | Nullable |               Default
-----------+-----------------------------+-----------+----------+--------------------------------------
 id        | integer                     |           | not null | nextval('students_id_seq'::regclass)
 name      | character varying(20)       |           | not null |
 age       | integer                     |           |          |
 locked    | boolean                     |           | not null | false
 create_at | timestamp without time zone |           | not null |
Indexes:
    "students_pkey" PRIMARY KEY, btree (id)

/* 表原始数据 */
 SELECT * FROM students;
 id | name | age | locked |      create_at
----+------+-----+--------+---------------------
  2 | Lily |  19 | f      | 2024-02-22 20:31:38
  1 | John |  20 | f      | 2024-02-22 20:31:38
  4 | Alen |     | f      | 2024-02-22 20:31:48
(3 rows)
```

## 查询数据

```sql
SELECT
  expression
  expression
  ......
FROM
  table_name

/* 表的所有数据 */
SELECT * FROM students;

/* 取指定列 */
SELECT id,name FROM students;
 id | name
----+------
  2 | Lily
  1 | John
  4 | Alen
(3 rows)

SELECT * FROM students
WHERE age IS NOT NULL;

/* 查询18、19、20岁的学生, 并从大到小排序 */
SELECT * FROM students
WHERE age IN (18,19,20)
ORDER BY age DESC;
```

## 插入数据

```sql
INSERT INTO table_name(column1, column2, …)
VALUES
  (value11, value12, …) [, (value21, value22, …), ...]
[ON CONFLICT conflict_target conflict_action]
[RETURNING expr];

/* 插入数据 */
INSERT INTO students (name, age, locked, create_at)
VALUES
  ('John', 18, false, '2024-02-22 20:31:38'),
  ('Lily', 19, false, '2024-02-22 20:31:38');
> INSERT 0 2

/* 插入数据并返回 */
INSERT INTO students (name, age, locked, create_at)
VALUES
  ('John', 18, false, '2024-02-22 20:31:38'),
  ('Lily', 19, false, '2024-02-22 20:31:38')
RETURNING name, create_at;

name |      create_at
----+---------------------
John | 2024-02-22 20:31:38
Lily | 2024-02-22 20:31:38

/* 查询数据 */
SELECT * FROM students;
 id | name | age | locked |      create_at
----+------+-----+--------+---------------------
  1 | John |  18 | f      | 2024-02-22 20:31:38
  2 | Lily |  19 | f      | 2024-02-22 20:31:38
```

## 删除数据

```sql
DELETE FROM table_name
[WHERE expression]
[RETURNING expr];;

DELETE FROM students WHERE id = 3;
> DELETE 1
```

## 更新数据

```sql
UPDATE [IGNORE] table_name
SET
    column_name1 = value1,
    column_name2 = value2,
    ...
[WHERE clause]
[RETURNING expr];

UPDATE students
SET
  age = 20
WHERE id = 1;
> UPDATE 1
```
