---
author: facsert
pubDatetime: 2023-05-23 15:28:43
title: Go Goroutine
slug: Go Goroutine
featured: false
draft: false
tags:
  - Go
description: "Go 协程"
---

<!--
 * @Author       : facsert
 * @Date         : 2023-05-23 15:28:43
 * @LastEditTime : 2023-10-11 22:01:43
 * @Description  : edit description
-->

## Table of Contents

## 协程

golang 支持并发和并行, 可以同时跑多个协程
golang 使用 `go <function name>()` 创建并执行协程
协程本质是一个函数

```go
import (
    . "fmt"
    "time"
    "sync"
)

var wg sync.WaitGroup                            // 定义 wg 辅助协程计数和执行

func main() {
    Println(time.Now())                          // 执行前打印时间

    wg.Add(2)                                    // 标记需要执行两个协程
    go cook()                                    // 新建协程, 类似于将 cook 放后台执行, main 直接接执行下一行
    go wash()                                    // 新建协程, 类似于将 wash 放后台执行, main 直接接执行下一行

    wg.Wait()                                    // 等待所有的协程执行完成
    Println(time.Now())                          // 所有协程执行完后打印时间
}

func cook() {
    time.Sleep(time.Second * 3)                  // sleep 3s
    Println("cook by machine use 3s")
    wg.Done()                                    // 标记协程执行完毕
}

func wash() {
    time.Sleep(time.Second * 2)
    Println("wash colse by machine use 2s")
    wg.Done()
}

> 2023-04-01 21:51:20.692681383 +0800 CST m=+0.000016384
> wash colse by machine use 2s
> cook by machine use 3s
> 2023-04-01 21:51:23.693482859 +0800 CST m=+3.000817861
```

golang 通过新建 2 个协程同时跑 main cook wash 函数, 3 个函数并行执行共花费 3s.
main 也是一个协程, main 中若不设置 wg.Wait() 等待其余协程完成, main 执行完成后会关闭所有协程

## channel

```go

```

### 无缓冲通道

通道容量为 0, 不能存值, 发送语句和结束语句需要都执行, 否则一方会一直等待另一方导致阻塞  
发送语句先执行, 则发送语句阻塞, 等待接收语句执行  
接受语句先执行, 则接受语句阻塞, 等待发送语句执行  
以上特性常用于协程同步, 无缓冲通道也被称为同步通道

```go
import (
    . "fmt"
    "time"
    "sync"
)

var wg sync.WaitGroup

func main() {
    Printf("start at %v\n", time.Now())
    ch := make(chan int, 0)                      // 初始化通道, 设置缓冲容量为 0
    wg.Add(1)
    go receive(ch)                               // 创建协程
    ch <- 3                                      // 向通道写入数据, 等待接收语句执行, 数据接收后执行下一句
    Printf("send at %v\n", time.Now())
}

func receive(c chan int) {
    time.Sleep(time.Second * 2)
    rec := <- c                                  // 接收通道数据, 若先执行则等待发送语句执行
    Printf("receive %d at %v\n", rec, time.Now())// 发送和接收执行同步
    wg.Done()
}

> start at 2023-04-01 22:52:35.842840438 +0800 CST m=+0.000018823
> receive 3 at 2023-04-01 22:52:37.843993741 +0800 CST m=+2.001172157
> send at 2023-04-01 22:52:37.844102485 +0800 CST m=+2.001280900
```

### 有缓冲通道

通道容量大于 0, 通道允许在容量未满时存值, 容量存满时变成无缓冲通道

```go
import (
    . "fmt"
    "time"
    "sync"
)

var wg sync.WaitGroup

func main() {
    Printf("start at %v\n", time.Now())
    ch := make(chan int, 2)                      // 初始化通道, 设置缓冲容量为 2
    wg.Add(2)
    go receive(ch)                               // 创建协程接收通道的值
    go send(ch)                                  // 创建协程向通道传值
    wg.Wait()
    Printf("over at %v\n", time.Now())
}

func send(ch chan int) {
    ch <- 1
    ch <- 2
    close(ch)                                    // 向通道传值结束后关闭通道
    Printf("send 1,2 at %v\n", time.Now())
    wg.Done()
}

func receive(ch chan int) {
    time.Sleep(time.Second * 1)
    rec := []int{}
    for i := range ch {                          // 通道关闭后, 使用 range 读取通道值
        rec = append(rec, i)
    }
    Printf("receive %v at %v\n", rec, time.Now())// 发送和接收执行同步
    wg.Done()
}

> start at 2023-04-02 12:20:34.69317803 +0800 CST m=+0.000016435
> send 1,2 at 2023-04-02 12:20:34.693231007 +0800 CST m=+0.000069413
> receive [1 2] at 2023-04-02 12:20:35.693311209 +0800 CST m=+1.000149614
> over at 2023-04-02 12:20:35.693371227 +0800 CST m=+1.000209634

```
