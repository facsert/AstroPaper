---
author: facsert
pubDatetime: 2023-07-10 14:24:30
title: Async
slug: Async
featured: false
draft: false
tags:
  - async
description: "异步详解"
---

<!--
 * @Author       : facsert
 * @Date         : 2023-07-10 14:24:30
 * @LastEditTime : 2023-07-28 12:01:48
 * @Description  : edit description
-->

## Table of Contents

- 简述
- 异步
- 多线程

## 简述

- 密集型操作
- 单线程与多线程
- 同步与异步

### 密集型操作

- I/O bound
- CPU bound

```bash

time |------- Device I/O -------|-- other --|
             long time           short time

data <- CPU <- data -> Memory <- data -> device(Disk NIC)
     ^          ^                 ^
    fast      slowly            slowly
```

I/O bound: I/O 密集操作, CPU 大部分时间在等硬盘和内存 I/O, CPU loading 低
I/O bond 因为外部设备瓶颈无法发挥 CPU 性能

解决方案: 多进程 多线程 异步

```bash
time |------ CPU operation ------|-- other --|
             long time             short time
```

CPU bound: CPU 密集操作, CPU 大部分时间处于运算中, CPU loading 高
I/O bond 因为 CPU 瓶颈无法发挥外部设备性能

解决方案: 多核心并行运算

### 单线程与多线程

单线程:
程序中只有一个执行流
资源占用小, 不会出现资源数据竞争

多线程:
程序中包含多个执行流, 多个执行流并行完成任务
多个线程之间共享进程内的数据与资源

```python
import time
from threading import Thread

def wait(name:str, delay:int):
    print(f"{name:<8} {delay} {time.strftime('%X')}")
    time.sleep(delay)
    print(f"{name:<8} {delay} {time.strftime('%X')}")

def main() -> None:
    first:Thread  = Thread(target=wait, args=('first', 4))
    second:Thread = Thread(target=wait, args=('second', 2))
    third:Thread  = Thread(target=wait, args=('third', 6))

    [p.start() for p in (first, second, third)]
    [p.join() for p in (first, second, third)]

if __name__ == '__main__':
    main()

first    4 14:26:15
second   2 14:26:15
third    6 14:26:15
second   2 14:26:17
first    4 14:26:19
third    6 14:26:21
```

### 同步与异步

同步:
同步执行代码时是顺序逐行执行的, 必须执行当前代码后才能继续执行下一行
同步代码有确定的执行顺序
同步代码遇到文件 I/O 或网络 I/O 会产生阻塞

异步:
执行遇到异步操作会直接返回, 继续执行后续代码, 待异步操作完成后, 主线程能收到消息
异步的执行顺序与代码顺序不一定一致
异步操作之间无数据依赖, 可以独立执行

```javascript

// 同步读取文件
const readSync = (name, file) => {
    console.log(`${name} ${(new Date()).toString()}`);
    require("fs").readFileSync(file);
    console.log(`${name} ${(new Date()).toString()}`);
}

// 异步读取文件
const readAsync = (name, file) => {
    console.log(`${name} ${(new Date()).toString()}`);
    require('fs').readFile(file, () => {
        console.log(`${name} ${(new Date()).toString()}`);
    });
    console.log(`${name} Read Finish`);
}

const main = () => {
    readSync("first ", "read/one_line_1.log")
    readSync("second", "read/one_line_2.log")
    readSync("third ", "read/one_line_3.log")

    readAsync("first ", "read/one_line_1.log")
    readAsync("second", "read/one_line_2.log")
    readAsync("third ", "read/one_line_3.log")
}

main()

// 同步读取 3 个文件消耗 30s
first  Wed Nov 23 2022 16:44:21 GMT+0800 (China Standard Time)
first  Wed Nov 23 2022 16:44:31 GMT+0800 (China Standard Time)
second Wed Nov 23 2022 16:44:31 GMT+0800 (China Standard Time)
second Wed Nov 23 2022 16:44:41 GMT+0800 (China Standard Time)
third  Wed Nov 23 2022 16:44:41 GMT+0800 (China Standard Time)
third  Wed Nov 23 2022 16:44:51 GMT+0800 (China Standard Time)

// 异步读取同样 3 个文件消耗 17s
first  Wed Nov 23 2022 16:44:51 GMT+0800 (China Standard Time)
first  Read Finish
second Wed Nov 23 2022 16:44:51 GMT+0800 (China Standard Time)
second Read Finish
third  Wed Nov 23 2022 16:44:51 GMT+0800 (China Standard Time)
third  Read Finish
third  Wed Nov 23 2022 16:45:01 GMT+0800 (China Standard Time)
second Wed Nov 23 2022 16:45:03 GMT+0800 (China Standard Time)
first  Wed Nov 23 2022 16:45:08 GMT+0800 (China Standard Time)
```

## 异步

- 多线程异步
- 单线程异步
- Python 多线程异步

### 多线程异步

```Go
package main

import (
    "fmt"
    "sync"
    "time"
)

var wg sync.WaitGroup

func Delay(name string, delay int) {
    defer wg.Done()
    fmt.Printf("%-8s %d %v\n", name, delay, time.Now())
    time.Sleep(time.Duration(delay) * time.Second)
    fmt.Printf("%-8s %d %v\n", name, delay, time.Now())
}

func main() {
    go Delay("first",  4)
    go Delay("second", 2)
    go Delay("third",  5)

    // time.Sleep(3 * time.Second)
    fmt.Println("Over")
    wg.Wait()
}


Over
third    6 2022-11-29 15:30:08.3114437 +0800 CST m=+0.000274101
first    4 2022-11-29 15:30:08.311496 +0800 CST m=+0.000326401
second   2 2022-11-29 15:30:08.3115808 +0800 CST m=+0.000411201
second   2 2022-11-29 15:30:10.3130423 +0800 CST m=+2.001872801
first    4 2022-11-29 15:30:12.3132763 +0800 CST m=+4.002106801
third    6 2022-11-29 15:30:14.3136864 +0800 CST m=+6.002517101


Over
third    6 2022-11-29 15:30:31.0126997 +0800 CST m=+0.000092501
first    4 2022-11-29 15:30:31.0128042 +0800 CST m=+0.000197101
second   2 2022-11-29 15:30:31.0128688 +0800 CST m=+0.000261701
second   2 2022-11-29 15:30:33.0141738 +0800 CST m=+2.001566701
first    4 2022-11-29 15:30:35.0143726 +0800 CST m=+4.001765501
third    6 2022-11-29 15:30:37.0147045 +0800 CST m=+6.002098001


third    6 2022-11-29 15:30:55.8741324 +0800 CST m=+0.000070801
second   2 2022-11-29 15:30:55.874294 +0800 CST m=+0.000232401
first    4 2022-11-29 15:30:55.8742926 +0800 CST m=+0.000231201
second   2 2022-11-29 15:30:57.8755269 +0800 CST m=+2.001465601
Over
first    4 2022-11-29 15:30:59.8752317 +0800 CST m=+4.001170301
third    6 2022-11-29 15:31:01.8756551 +0800 CST m=+6.001593901
```

主线程跳过异步操作, 新开线程执行跳过的操作, 使主线程运行达到异步效果
主线程与其它线程独立, 互不影响

### 单线程异步

```javascript
// 异步延时
const wait = async (name, delay) => {
    console.log(`${name} ${delay} ${(new Date()).toString()}`);
    setTimeout(() => {
        console.log(`${name} ${delay} ${(new Date()).toString()}`);
    }, delay*1000);
    console.log(`${name} Read Finish`)
}

const recordSync = (name, delay) => {
    console.log(`${name} ${delay} ${(new Date()).toString()}`);
    let sum = 0;
    for (let i=0; i<delay; i++) {
        sum += i;
    };
    console.log(`${name} ${delay} ${(new Date()).toString()}`);
}

const main = () => {
    wait("first ", 4);
    wait("second", 2);
    wait("third ", 6);
    // recordSync("third ", 4000000000)
    console.log("Over")
}

main()

first  4 Tue Nov 22 2022 11:04:58 GMT+0800 (China Standard Time)
first  Read Finish
second 2 Tue Nov 22 2022 11:04:58 GMT+0800 (China Standard Time)
second Read Finish
third  6 Tue Nov 22 2022 11:04:58 GMT+0800 (China Standard Time)
third  Read Finish
Over
second 2 Tue Nov 22 2022 11:05:00 GMT+0800 (China Standard Time)
first  4 Tue Nov 22 2022 11:05:02 GMT+0800 (China Standard Time)
third  6 Tue Nov 22 2022 11:05:04 GMT+0800 (China Standard Time)


first  4 Tue Nov 29 2022 10:32:40 GMT+0800 (China Standard Time)
first  Read Finish
second 2 Tue Nov 29 2022 10:32:40 GMT+0800 (China Standard Time)
second Read Finish
third  6 Tue Nov 29 2022 10:32:40 GMT+0800 (China Standard Time)
third  Read Finish
third  4000000000 Tue Nov 29 2022 10:32:40 GMT+0800 (China Standard Time)
third  4000000000 Tue Nov 29 2022 10:32:45 GMT+0800 (China Standard Time)
Over
second 2 Tue Nov 29 2022 10:32:45 GMT+0800 (China Standard Time)
first  4 Tue Nov 29 2022 10:32:45 GMT+0800 (China Standard Time)
third  6 Tue Nov 29 2022 10:32:46 GMT+0800 (China Standard Time)
```

异步延时函数由于主线程耗时过多, 未能在设定时间点执行对应操作

异步操作必须等主线程执行栈内所有指令完成后才能执行
主线程执行进度会影响到后续异步操作

定时触发(setTimeout setInterval)是由浏览器的定时器线程执行的定时计数，然后在定时时间把定时处理函数的执行请求插入到JS执行队列的尾端

### Python 多线程异步

```python
import asyncio
import time

def record(name: str, end: int, ) -> None:
    print(f"{name:<8} {end} {time.strftime('%X')}")
    sum = 0
    for i in range(end):
        sum += i
    print(f"{name:<8} {end} {time.strftime('%X')}")

def wait(name:str, delay:int) -> None:
    print(f"{name:<8} {delay} {time.strftime('%X')}")
    time.sleep(delay)
    print(f"{name:<8} {delay} {time.strftime('%X')}")

async def main():
    await asyncio.gather(
        asyncio.to_thread(wait, 'first',  4),
        asyncio.to_thread(wait, 'second', 2),
        asyncio.to_thread(wait, 'third',  6),
    )

    await asyncio.gather(
        asyncio.to_thread(record, 'first',  60000000),
        asyncio.to_thread(record, 'second', 10000000),
        asyncio.to_thread(record, 'third',  30000000),
    )

if __name__ == 'main':
    asyncio.run(main())

# 异步执行 3 个演示函数和 3 千万量级的运算
first    4 16:43:40
second   2 16:43:40
third    6 16:43:40
second   2 16:43:42
first    4 16:43:44
third    6 16:43:46
first    40000000 16:43:46
second   20000000 16:43:46
third    60000000 16:43:46
second   20000000 16:43:51
first    40000000 16:43:53
third    60000000 16:43:55

# 执行单个千万量级的运算
first    40000000 16:45:38
first    40000000 16:45:41

second   20000000 16:46:13
second   20000000 16:46:15

third    60000000 16:46:32
third    60000000 16:46:36
```

> 由于 GIL 的存在, asyncio.to_thread() 通常只能被用来将 IO 密集型函数变为非阻塞的
> 对于会释放 GIL 的扩展模块或无此限制的替代性 Python 实现来说, asyncio.to_thread() 也可被用于 CPU 密集型函数.(多进程, 或无 GIL 的 Python)

## 多线程

- Javascript
- Python
- Golang

### Javascript

Javascript 仅支持单线

```javascript

const record = (name, delay) => {
    console.log(`${name} ${delay} ${(new Date()).toString()}`);
    let promise = new Promise((resolve) => {
        let sum = 0;
        for (let i=0; i<delay; i++) {
            sum += i;
        };
        resolve(`${name} ${delay} ${(new Date()).toString()}`);
    });
    console.log(`${name} Finish`)
    return promise;
};

const main = () => {
    record("first ", 3000000000).then(msg => console.log(msg));
    record("second", 2000000000).then(msg => console.log(msg));
    record("third ", 4000000000).then(msg => console.log(msg));
    console.log("Over")
}

main()

// 异步执行 3 个十亿量级运算
first  3000000000 Wed Nov 23 2022 15:23:28 GMT+0800 (China Standard Time)
first  Finish
second 2000000000 Wed Nov 23 2022 15:23:31 GMT+0800 (China Standard Time)
second Finish
third  4000000000 Wed Nov 23 2022 15:23:33 GMT+0800 (China Standard Time)
third  Finish
Over
first  3000000000 Wed Nov 23 2022 15:23:31 GMT+0800 (China Standard Time)
second 2000000000 Wed Nov 23 2022 15:23:33 GMT+0800 (China Standard Time)
third  4000000000 Wed Nov 23 2022 15:23:38 GMT+0800 (China Standard Time)


// 异步执行 1 个十亿量级的运算
third  4000000000 Wed Nov 23 2022 15:24:04 GMT+0800 (China Standard Time)
third  Finish
Over
third  4000000000 Wed Nov 23 2022 15:24:09 GMT+0800 (China Standard Time)

// 同步执行 1 个十亿量级的运算
third  4000000000 Wed Nov 23 2022 15:26:31 GMT+0800 (China Standard Time)
third  4000000000 Wed Nov 23 2022 15:26:36 GMT+0800 (China Standard Time)
Over
```

JS的单线程是指一个浏览器进程中只有一个JS的执行线程，同一时刻内只会有一段代码在执行
JS 异步请求是浏览器的两个常驻线程共同完成的: JS 执行线程和事件触发线程  
JS的执行线程发起异步请求，事件触发线程监视到之前的发起的HTTP请求已完成，它就会把完成事件插入到JS执行队列的尾部等待JS处理

### Python

Python 支持多线程

```python
import time
from threading import Thread

def record(name: str, end: int, ) -> None:
    print(f"{name:<8} {end} {time.strftime('%X')}")
    # sum(range(end), 0)
    sum = 0
    for i in range(end):
        sum += i
    print(f"{name:<8} {end} {time.strftime('%X')}")

def main():
    first:Thread  = Thread(target=record, args=('first',  40000000))
    second:Thread = Thread(target=record, args=('second', 20000000))
    third:Thread  = Thread(target=record, args=('third',  60000000))

    [p.start() for p in (first, second, third)]
    [p.join() for p in (first, second, third)]

if __name__ == '__main__':
    main()


first    40000000 15:27:18
third    60000000 15:27:18
second   20000000 15:27:24
first    40000000 15:27:28
third    60000000 15:27:29


first    40000000 15:27:40
first    40000000 15:27:43

second   20000000 15:27:58
second   20000000 15:27:59

third    60000000 15:28:13
third    60000000 15:28:17
```

由于进程中 GIL 存在, 每个进程同一时间点仅允许一个线程执行
Python 单个进程中的多线程实际是多个线程快速来回切换, 不省 CPU 计算时间

```python
import time
from multiprocessing import  Process

def record(name: str, end: int, ) -> None:
    print(f"{name:<8} {end} {time.strftime('%X')}")
    # sum(range(end), 0)
    sum = 0
    for i in range(end):
        sum += i
    print(f"{name:<8} {end} {time.strftime('%X')}")

def main():
    first:Thread  = Process(target=record, args=('first',  40000000))
    second:Thread = Process(target=record, args=('second', 20000000))
    third:Thread  = Process(target=record, args=('third',  60000000))

    [p.start() for p in (first, second, third)]
    [p.join() for p in (first, second, third)]

if __name__ == '__main__':
    main()


first    40000000 15:22:03
second   20000000 15:22:03
third    60000000 15:22:03
second   20000000 15:22:04
first    40000000 15:22:06
third    60000000 15:22:07

first    40000000 15:23:55
first    40000000 15:23:58

second   20000000 15:25:07
second   20000000 15:25:08

third    60000000 15:24:32
third    60000000 15:24:36
```

### Golang

Golang 支持多线程, 使用 GPM 模型自动化分配协程到线程上执行

```Go
package main

import (
    "fmt"
    "sync"
    "time"
)

var wg sync.WaitGroup

func Sum(name string, end int) {
    defer wg.Done()
    fmt.Printf("%-8s %d %v\n", name, delay, time.Now())
    sum := 0
    for i := 0; i < end; i++ {
        sum += i
    }
    fmt.Printf("%-8s %d %v\n", name, delay, time.Now())
}

func main() {
    wg.Add(3)
    go Sum("first",  15000000000)
    go Sum("second", 10000000000)
    go Sum("third",  20000000000)

    fmt.Println("Over")
    wg.Wait()
}

// 异步执行 3 个百亿量级运算
third    20000000000 2022-11-22 10:51:36.4803571 +0800 CST m=+0.000173601
second   10000000000 2022-11-22 10:51:36.4804665 +0800 CST m=+0.000283101
first    15000000000 2022-11-22 10:51:36.4804039 +0800 CST m=+0.000220401
second   10000000000 2022-11-22 10:51:41.7277742 +0800 CST m=+5.247590701
first    15000000000 2022-11-22 10:51:43.8958834 +0800 CST m=+7.415699901
third    20000000000 2022-11-22 10:51:45.9174494 +0800 CST m=+9.437265801

// 异步执行单个百亿量级运算
third    20000000000 2022-11-22 10:51:00.7876033 +0800 CST m=+0.000169201
third    20000000000 2022-11-22 10:51:08.1227567 +0800 CST m=+7.335322701
```
