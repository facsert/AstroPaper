---
author: facsert
pubDatetime: 2023-07-28 09:31:54
title: Go SSH
postSlug: ""
featured: false
draft: false
tags:
  - Go
  - ssh
description: "Go SSH 连接"
---

<!--
 * @Author       : facsert
 * @Date         : 2023-07-28 09:31:54
 * @LastEditTime : 2023-07-28 11:35:22
 * @Description  : edit description
-->

Go 实现 ssh 连接远端并发送命令执行  
`golang.org/x/crypto/ssh`

## 创建项目

创建 sshClient 项目, 添加 ssh 库

```bash
 $ mkdir sshClient
 $ cd sshClient && go mod init sshClient
 $ touch main.go
 $ go get -u "golang.org/x/crypto/ssh"

 $ ls -l
 > total 12
 > -rw-r--r-- 1 root root 115 Jul 27 02:58 go.mod
 > -rw-r--r-- 1 root root 312 Jul 27 02:58 go.sum
 > -rw-r--r-- 1 root root 973 Jul 27 04:20 main.go
```

### 流程

```bash
# ssh.Dial 配置 server 信息生成 client 对象
# client 创建一个 session 执行 linux 命令并获取返回值

      server config               new                      command
ssh.Dial ---> client(*ssh.Client) ---> session(*ssh.Session) ---> server
                         1                      N

# 单个 client 可以创建多个 session 执行 linux 指令
# session 执行指令失败或者命令返回值不为 0 则报错
```

## 设计

```go
type Server struct {
    host     string
    port     string
    username string
    password string
    timeout  int32
    command  string
}

func Connect(server Server) (*ssh.Client, error) { ... }
func Run(client *ssh.Client, command string) (string, error) { ... }
```

## 实现

`func Connect(server Server) (*ssh.Client, error)`  
接受一个 Server 结构体, 通过 server 属性生成 \*ssh.Client 对象

```go
func Connect(server Server) (*ssh.Client, error) {
    config := ssh.ClientConfig{
        User:            server.username,
        Auth:            []ssh.AuthMethod{ssh.Password(server.password)},
        Timeout:         time.Duration(server.timeout) * time.Second,
        HostKeyCallback: ssh.InsecureIgnoreHostKey(),
    }

    sshClient, err := ssh.Dial("tcp", fmt.Sprintf("%s:%s", server.host, server.port), &config)
    if err != nil {
        fmt.Printf("Connect host %s error: %v\n", server.host, err)
        return nil, err
    }
    return sshClient, nil
}
```

`func Run(client *ssh.Client, command string) (string, error) { ... }`  
接受 Connect 返回的 client 对象, 和执行的 linux 命令, 返回执行结果

```go
func Run(client *ssh.Client, command string) (string, error) {
    session, err := client.NewSession()
    if err != nil {
        return fmt.Sprintf("create session failed: %v\n", err), err
    }
    defer session.Close()

    output, err := session.CombinedOutput(command)
    if err != nil {
        return string(output), err
    }
    return string(output), nil
}
```

## 代码

```go
package main

import (
    "fmt"
    "time"

    "golang.org/x/crypto/ssh"
)

type Server struct {
    host     string
    port     string
    username string
    password string
    timeout  int32
    command  string
}

func main() {
    server := Server {
        host    : "127.0.0.1",
        port    : "22",
        username: "root",
        password: "admin",
        timeout : 60,
        command : "date",
    }

    client, err := Connect(server)
    if err != nil {
        panic(fmt.Sprintf("Connect %s failed!", server.host))
    }

    output, err := Run(client, server.command)
    if err != nil {
        panic(fmt.Sprintf("%s run error: %v\n", server.command, err))
    }
    fmt.Println(output)
}

func Connect(server Server) (*ssh.Client, error) {
    config := ssh.ClientConfig{
        User:            server.username,
        Auth:            []ssh.AuthMethod{ssh.Password(server.password)},
        Timeout:         time.Duration(server.timeout) * time.Second,
        HostKeyCallback: ssh.InsecureIgnoreHostKey(),
    }

    sshClient, err := ssh.Dial("tcp", fmt.Sprintf("%s:%s", server.host, server.port), &config)
    if err != nil {
        fmt.Printf("Connect host %s error: %v\n", server.host, err)
        return nil, err
    }
    return sshClient, nil
}

func Run(client *ssh.Client, command string) (string, error) {
    session, err := client.NewSession()
    if err != nil {
        return fmt.Sprintf("create session failed: %v\n", err), err
    }
    defer session.Close()

    output, err := session.CombinedOutput(command)
    if err != nil {
        return string(output), err
    }
    return string(output), nil
}
```

## 拓展

模拟实现终端

```go
func Termiunal(client *ssh.Client) {
    session, err := client.NewSession()
    if err != nil {
        panic(fmt.Sprintf("create session failed: %v\n", err))
    }
    defer session.Close()

    session.Stdout = os.Stdout                   // 会话输出关联到系统标准输出设备
    session.Stderr = os.Stderr                   // 会话错误输出关联到系统标准错误输出设备
    session.Stdin = os.Stdin                     // 会话输入关联到系统标准输入设备
    modes := ssh.TerminalModes{
        ssh.ECHO:          0,                    // 禁用回显（不重复显示执行命令 0禁用，1启动）
        ssh.TTY_OP_ISPEED: 14400,                // input speed = 14.4kbaud
        ssh.TTY_OP_OSPEED: 14400,                // output speed = 14.4kbaud
    }

    if err = session.RequestPty("linux", 32, 160, modes); err != nil {
        panic(fmt.Sprintf("request pty error: %v\n", err))
    }
    if err = session.Shell(); err != nil {
        panic(fmt.Sprintf("start shell error: %v", err))
    }
    if err = session.Wait(); err != nil {
        panic(fmt.Sprintf("return error: %v", err))
    }
}
```
