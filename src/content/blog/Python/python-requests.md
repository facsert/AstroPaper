---
author: facsert
pubDatetime: 2023-12-13 20:57:56
title: Python requests
slug: Python requests
featured: false
draft: false
tags:
  - Python
  - requests
description: "Python HTTP 模块 requests"
---

<!--
 * @Author: facsert
 * @Date: 2023-12-13 20:57:56
 * @LastEditTime: 2023-12-13 20:58:57
 * @LastEditors: facsert
 * @Description:
-->

requests 是一个简单强大的 http请求库，支持同步和异步

## Table of Contents

## 安装

```bash
 $ python -m pip install requests
 $ python -c "import requests" && echo $?
 > 0
```

## HTTP

`requests` 是一个 python 的 http 库, 它可以用来发送 http 请求, 并接收 http 响应  
HTTP 的全称是 HyperText Transfer Protocol (超文本传输协议)的缩写，是一种建立在 TCP 上的无状态连接  
HTTP 是互联网的基础协议，用于客户端与服务器之间的通信，它规定了客户端和服务器之间的通信格式，包括请求与响应的格式  

客户端通过地址链接生成 HTTP 报文, 并发送给服务器, 服务器根据请求方法，向客户端返回响应  

```bash
# 请求 URL
http://localhost:8001/node/get?id=1              

# HTTP 报文主要信息
Request URL: http://localhost:8001/node/get?id=1 # 请求 URL
Request Method: GET                              # 请求方法
Status Code: 200 OK                              # 响应状态码
Remote Address: 127.0.0.1:8001
Referrer Policy: strict-origin-when-cross-origin

# chrome General 请求报文所有信息
accept: application/json                         # 客户端接收的数据格式
Accept-Encoding: gzip, deflate, br               # 客户端接收的数据压缩格式
Accept-Language: zh-CN,zh;q=0.9                  # 客户端接收的语言                         
Cache-Control: no-cache  
Connection: keep-alive                           # 连接类型
Host: localhost:8001                             # 服务器地址
Pragma: no-cache
Referer: http://localhost:8001/
sec-ch-ua: "Chromium";v="110", "Not A(Brand";v="24", "Google Chrome";v="110"
sec-ch-ua-mobile: ?0
sec-ch-ua-platform: "Windows"
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/110.0.0.0 Safari/537.36

# HTTP 响应
content-length: 39                               # 响应数据长度
content-type: application/json                   # 响应数据格式
date: Wed, 03 Jan 2024 13:48:14 GMT              # 响应时间
server: uvicorn
```

## 发送请求

request 支持 4 种基本请求方法: GET, POST, PUT, DELETE

### GET

GET 请求可在 url 中携带参数, 以 `?` 分界, `&` 分割多个参数  
如: `http://localhost:8001/node/get?name=lily&age=18`

```python
import requests

r = requests.get('http://localhost:8001/node/get?name=lily')
r = requests.get('http://localhost:8001/node/get', params={'name': 'lily'})

print(r.status_code)
print(r.text)
```

requests.get 参数说明:

- url: 请求的 URL
- params: 请求的 URL 中的参数
- data: 请求的 body 数据
- headers: 请求的 header 数据
- cookies: 请求的 cookie 数据
- files: 请求的上传文件数据
- auth: 认证信息
- timeout: 超时时间
- allow_redirects: 是否允许重定向
- proxies: 代理服务器

### POST

POST 请求一般用于向服务器发送数据, 如: 表单提交, 文件上传等  
POST 请求在请求体中发送数据, 如: `http://localhost:8001/node/post`

```python
import requests

r = requests.post('http://localhost:8001/node/post', data={'name': 'lily'})
```

requests.post 参数说明:

- url: 请求的 URL
- data: 请求的 body 数据
- json: 请求的 JSON 数据
- headers: 请求的 header 数据
- cookies: 请求的 cookie 数据
- files: 请求的上传文件数据
- auth: 认证信息
- timeout: 超时时间
- allow_redirects: 是否允许重定向

## Response

### Response.text

返回响应体的文本内容

```python
import requests

r = requests.get('http://localhost:8001/node/get?name=lily')
print(r.text)
```

### Response.json

返回响应体的 JSON 数据

```python
import requests

r = requests.get('http://localhost:8001/node/get?name=lily')
print(r.json())
```

### Response.status_code

返回响应状态码

```python
import requests

r = requests.get('http://localhost:8001/node/get?name=lily')
print(r.status_code)
```

### Response.headers

返回响应头

```python
import requests

r = requests.get('http://localhost:8001/node/get?name=lily')
print(r.headers)
```

### Response.url

返回请求的 URL

```python
import requests

r = requests.get('http://localhost:8001/node/get?name=lily')
print(r.url)
```

### Response.cookies

返回响应的 cookies

```python
import requests

r = requests.get('http://localhost:8001/node/get?name=lily')
print(r.cookies)
```

### Response.encoding

返回响应的编码格式

```python
import requests

r = requests.get('http://localhost:8001/node/get?name=lily')
print(r.encoding)
```

### Response.raise_for_status

如果响应状态码不是 200, 则抛出异常

```python
import requests

r = requests.get('http://localhost:8001/node/get?name=lily')
r.raise_for_status()
```

### Response.content

返回响应体的二进制内容

```python
import requests

r = requests.get('http://localhost:8001/node/get?name=lily')
print(r.content)
```

### Response.iter_lines

返回响应体的迭代器, 迭代器每迭代一次, 就返回一行内容

```python
import requests

r = requests.get('http://localhost:8001/node/get?name=lily')
for line in r.iter_lines():
    print(line)
```

### Response.iter_content

返回响应体的迭代器, 迭代器每迭代一次, 就返回一部分内容

```python
import requests

r = requests.get('http://localhost:8001/node/get?name=lily')
for chunk in r.iter_content(chunk_size=1024):
    print(chunk)

```

### Response.close

关闭响应体

```python
import requests

r = requests.get('http://localhost:8001/node/get?name=lily')
r.close()
```

### Response.raw

返回原始的响应对象

```python
import requests

r = requests.get('http://localhost:8001/node/get?name=lily')
print(r.raw)
```

## 文件

requests 支持文件上传和下载

### 上传文件

```python
import requests

files = {'file': open('test.txt', 'rb')}
r = requests.post('http://localhost:8001/node/upload', files=files)

# 上传文件, 并指定文件名
files = {'file': ('test.txt', open('test.txt', 'rb'))}
r = requests.post('http://localhost:8001/node/upload', files=files)
```

### 下载文件

```python
import requests

# 小文件下载
r = requests.get('http://localhost:8001/node/download')
with open('test.txt', 'wb') as f:
    f.write(r.content)

# 流式下载, 边下边存, 适合大文件
r = requests.get('http://localhost:8001/node/download')
with open('test.txt', 'wb') as f:
    for chunk in r.iter_content(1024):
        f.write(chunk)
```
