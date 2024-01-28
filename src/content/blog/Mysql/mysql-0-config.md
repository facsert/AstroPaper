---
author: facsert
pubDatetime: 2023-09-12 20:17:32
title: Mysql Configuration
slug: Mysql Configuration
featured: false
draft: false
tags:
  - Mysql
description: "Mysql 基本配置"
---

<!--
 * @Author: facsert
 * @Date: 2023-09-12 20:17:32
 * @LastEditTime: 2023-11-01 20:05:30
 * @LastEditors: facsert
 * @Description:
-->

## Table of Contents

通过 docker 拉取 mysql 镜像  
根据镜像生成容器, 配置文件和端口映射, 设置 root 密码  
进入容器, 使用 root 用户打开 mysql 命令行

```bash
 $ docker pull mysql
 $ docker images
 > mysql latest 99afc808f15b 4 weeks ago 577MB

 $ docker run -d -v /root/Desktop/Mysql:/root -p 3306:3306 --name sql -e MYSQL_ROOT_PASSWORD=111111 mysql
 $ docker exec -it sql bash

```

```bash
> mysql -u root -p
> SHOW DATABASES;
mysql> show DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.00 sec)

> CREATE DATABASE sql;
Query OK, 1 row affected (0.01 sec)

> USE sql;
Database changed
```
