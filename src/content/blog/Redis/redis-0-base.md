---
author: facsert
pubDatetime: 2024-03-05 14:18:43
title: Redis Configuration
slug: Redis Configuration
featured: false
draft: false
tags:
  - Redis
description: "Redis 基本配置"
---

## Table of Contents

## 安装

docker 安装 docker-stack(自带 redisinsight), 便于 web 界面修改和查看数据

```bash
 $ docker pull redis/redis-stack

 $ docker run -d --name redis-stack -v /root/Desktop/redis/data:/data -p 6379:6379 -p 8001:8001 redis/redis-stack:latest

 $ docker ps
 > CONTAINER ID IMAGE                    COMMAND          CREATED      STATUS      PORTS                                                                                 NAMES
 > 0f52fb6bad6e redis/redis-stack:latest "/entrypoint.sh" 3 hours ago  Up 3 hours  0.0.0.0:6379->6379/tcp, :::6379->6379/tcp, 0.0.0.0:8001->8001/tcp, :::8001->8001/tcp  redis-stack

 # --name: 容器名
 # -v: linux 下路径映射容器内 /data (/data 是容器存放数据位置)
 # -p: 端口映射, 6379 是 redis 端口, 8001 是 redisinsight web 端口
```

浏览器打开 `http://localhost:8001` 进入 redisinsight 界面

## 数据类型

Redis 命令不区分大小写, 键值对是大小写敏感

### String

Redis 数据库中, 字符串数据就是简单的键值对, key-value

```bash
> SET test "hello redis"
"OK"

> GET test
"hello redis"
```

### Hash

Hash 是多个键值对的集合{k1-v1, k2-v2}

```bash
# 创建 students 字段, 添加键值对
> HSET students "John" 18
(integer) 1

# students 添加键值对
> HSET students "Lily" 18
(integer) 1

# 获取键值对的值
> HGET students John
"18"

# 获取所有键值对
> HGETALL students
1) "John"
2) "18"
3) "Lily"
4) "18"
```

### List

### Set

### Json

### Stream
