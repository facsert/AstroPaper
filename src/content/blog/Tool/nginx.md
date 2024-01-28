---
author: facsert
pubDatetime: 2022-01-19 21:51:23
title: nginx
slug: nginx
featured: false
draft: false
tags:
  - nginx
description: "nginx"
---

nginx 是一个 Web 服务器，它支持 HTTP、HTTPS、SPDY、SPDY/3、QUIC、WebSockets、SSE、SSE/2、SSE/3、SSE/4、SSE/5、SSE/6、SSE/7、SSE/8、SSE/9、SSE/10、SSE/11、SSE/12、SSE/13、SSE/

- 正向代理: 客户端配置正向代理, 通过代理服务器将请求转发到服务器(可隐藏客户端信息)
- 反向代理: 在服务端配置反向代理, 通过代理服务器接收客户端请求(可隐藏服务端信息)
- 负载均衡: 反向代理时, 单个代理服务对应多个后端服务, 根据算法转发请求
- 动静分离: 将静态资源(nginx)和动态资源(后端)分离, 以提高性能

## Table of Contents

## nginx 安装

[nginx](http://nginx.org/) 官网

安装包安装

nginx 是由 C 编写, 需要使用相关依赖编译器编译

```bash
 $ apt install make zlib zlib-devel gcc-c++ libtool openssl openssl-devel -y
 $ tar -xvf nginx-1.25.3.tar.gz
 $ cd nginx-1.25.3
 $ ./configure
 $ make && make install
```

包管理工具安装

```bash
 $ apt install nginx
 $ systemctl start nginx
```

docker 镜像安装

```bash
 $ docker pull nginx
 $ docker run -d -p 80:80 -p 443:443 --name nginx nginx
```

## nginx 配置

nginx 配置文件: /etc/nginx/nginx.conf

### 静态文件

将服务器指定路径映射到 nginx 端口

```bash
http {
    server {
        listen       80;
        server_name  localhost;
        client_max_body_size 1024M;

        # 默认location
        location / {
            root   /usr/local/;
            index  index.html index.htm;
        }
    }
}
```

http: 服务配置, 可以定义多个 server 块
listen: 服务器监听的端口
server_name: 服务器名称, 可以是域名或 IP 地址
location: 服务器路径, / 表示任意路径
root: 服务器路径, 用于指定静态文件的路径
index: 服务器路径, 用于指定静态文件的索引文件

### 反向代理

```bash
http {
    server {
        listen       80;
        server_name  localhost;
        client_max_body_size 1024M;

        location / {
            proxy_pass http://127.0.0.1:8080;
        }
    }
}
```

### 负载均衡

```bash

upstream backend {
    server 127.0.0.1:8080;
    server 127.0.0.1:8081;
    server 127.0.0.1:8082;
}

http {
    server {
        listen       80;
        server_name  localhost;
        client_max_body_size 1024M;
        
        location / {
            proxy_pass http://backend;
        }
    }
    }
}
```

负载均衡策略

- round_robin: 默认, 依次分配请求
- ip_hash: 根据客户端 IP 地址分配请求
- weighted: 根据权重分配请求
