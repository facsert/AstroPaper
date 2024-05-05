---
author: facsert
pubDatetime: 2024-04-06 19:15:19
title: Go grpc
slug: Go grpc
featured: false
draft: false
tags:
  - Go
  - grpc
description: "Go Web 框架 grpc"
---

## Table of Contents

## 安装

```bash
 $ go install google.golang.org/protobuf/cmd/protoc-gen-go@v1.28
 $ go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@v1.2

 # 添加环境变量 GOPATH
 $ export PATH="$PATH:$(go env GOPATH)/bin"
 $ echo $PATH | xargs -d ':' -n1 |grep go
 > /usr/local/go/bin

 # 确认已安装
 $ protoc --version
 > libprotoc 3.20.3

 $ protoc-gen-go --version
 > protoc-gen-go v1.28.1

 $ protoc-gen-go-grpc --version
 > protoc-gen-go-grpc 1.2.0
```

## 介绍

grpc 是一个高性能、开源和通用的 RPC 框架，由 Google 团队开发，用于在分布式系统中进行 RPC 调用
使用 hello.proto 生成服务端和客户端代码

```go
syntax = "proto3";

// 客户端指定 client/pb 作为包名
option go_package = "server/pb";

package pb;

message HelloRequest {
    string name = 1;
}

message HelloResponse {
    string message = 1;
}

service Hello {
    rpc SayHello(HelloRequest) returns (HelloResponse) {}
}
```

```bash
server
├── go.mod
├── go.sum
├── main.go
└── pb
    ├── hello.pb.go
    ├── hello.proto
    └── hello_grpc.pb.go

client
├── go.mod
├── go.sum
├── main.go
└── pb
    ├── hello.pb.go
    ├── hello.proto
    └── hello_grpc.pb.go

# 在服务端和客户端项目根目录执行命令, 按 protoc 生成源码
protoc --go_out=. --go_opt=paths=source_relative \
    --go-grpc_out=. --go-grpc_opt=paths=source_relative \
    pb/hello.proto
```

服务端代码 `server/main.go`  
服务端安装 grpc 库 `go get google.golang.org/grpc@latest`  
初始化服务端代码 `go mod init server`

```go
package main

import (
    "fmt"
    "context"
    "net"
    "google.golang.org/grpc"

    // 引入 pb(protoc 生成的源码)
    "server/pb"
)

// pb 的方法名称与 proto 文件中定义的 rpc 相关
type server struct {
    pb.UnimplementedHelloServer
}

func (s *server) SayHello(ctx context.Context, in *pb.HelloRequest) (*pb.HelloResponse, error) {
    fmt.Println("Received: ", in.GetName())
    return &pb.HelloResponse{Message: "Hello " + in.GetName()}, nil
}

func main() {
    lis, err := net.Listen("tcp", ":50051")
    if err != nil {
        fmt.Println("failed to listen: ", err)
    }
    s := grpc.NewServer()
    pb.RegisterHelloServer(s, &server{})
    fmt.Println("server listening at ", lis.Addr())
    if err := s.Serve(lis); err != nil {
        fmt.Println("failed to serve: ", err)
    }
}
```

```go
package main

import (
    "context"
    "flag"
    "log"
    "time"

    "grpc_client/pb"

    "google.golang.org/grpc"
    "google.golang.org/grpc/credentials/insecure"
)

const (
    defaultName = "world"
)

var (
    // 50051 是服务端监听的端口
    addr = flag.String("addr", "127.0.0.1:50051", "the address to connect to")
    name = flag.String("name", defaultName, "Name to greet")
)

func main() {
    flag.Parse()
    // 连接到server端，此处禁用安全传输
    conn, err := grpc.NewClient(*addr, grpc.WithTransportCredentials(insecure.NewCredentials()))
    if err != nil {
        log.Fatalf("did not connect: %v", err)
    }
    defer conn.Close()
    c := pb.NewHelloClient(conn)

    // 执行RPC调用并打印收到的响应数据
    ctx, cancel := context.WithTimeout(context.Background(), time.Second)
    defer cancel()
    r, err := c.SayHello(ctx, &pb.HelloRequest{Name: *name})
    if err != nil {
        log.Fatalf("could not greet: %v", err)
    }
    log.Printf("Greeting: %s", r.GetMessage())
}
```

```bash
# 编译服务端和客户端代码
go build

./server

./client
> 2024/04/06 19:12:17 Greeting: Hello world
```
