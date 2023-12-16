---
author: facsert
pubDatetime: 2023-04-02 15:28:43
title: Go File IO
postSlug: ""
featured: false
draft: false
tags:
  - Go
description: "Go 文件 IO"
---

<!--
 * @Author       : facsert
 * @Date         : 2023-04-02 15:28:43
 * @LastEditTime : 2023-07-28 11:37:54
 * @Description  : edit description
-->

## read file

golang 通过 os 对文件读写
`io/ioutil` 读写文件调用的是 os 的方法

```go

import {
    . "fmt"
    "io"
    "bufio"
}

func read(fileName string) string {                                  // 快速读取文件全部内容
    content, err := os.ReadFile(fileName)
    if  err != nil {
        panic(Sprintf("read %s failed: %s", fileName, err))
    }
    return string(content)
}

func readLine(fileName string) []string {                            // 逐行读取文件, 返回字符串切片, 切片元素结尾无换行符号
    file, err := os.OpenFile(fileName, os.O_RDONLY|os.O_CREATE, 0666)
    if err != nil {
        panic("read file error: ", err)
    }
    defer file.Close()

    buf := bufio.NewReader(file)
    content := []string{}
    for {
        line, _, err := buf.ReadLine()
        if err == io.EOF {
            break
        }
        content = append(content, string(line))
    }
    return content
}

func readByte(fileName string, length int) string {                  // 每次读取固定长度字符串
    file, err := os.OpenFile(fileName, os.O_RDONLY|os.O_CREATE, 0666)
    if err != nil {
        Println("read file error: ", err)
    }
    defer file.Close()

    buf := make([]byte, length)
    content := []byte{}
    for {
        _, err := file.Read(buf)
        if err == io.EOF {
            break
        }
        content = append(content, buf...)
    }
    return string(content)
}

```

## write file

文件写入分两种: 覆盖写入, 追加写入
文件写入函数本质是对 os.OpenFile 的封装

```go
func write(fileName, s string) {                                     // 文件覆盖写入, 文件不存在则自动新建
    if err := os.WriteFile(fileName, []byte(s), 0666); err != nil {  // WriteFile 是对 OpenFile 的封装
        panic(Sprintf("write error: %v\n", err))
    }
}

func writeInit(fileName, s string) {                                 // 覆盖写入文件, 文件不存在则创建
    file, err := os.OpenFile(fileName, os.O_CREATE|os.O_TRUNC|os.O_WRONLY, 0666)
    if err != nil {
        panic(Sprintln("open file failed"))
    }
    defer file.Close()

    if _, err := file.Write([]byte(s)); err != nil {                 // 也可用 file.WriteString(s), 本质也是调用 file.Write()
        panic(Sprintf("write file failed: %v\n", err))
    }
}

func writeAdd(fileName, s string) {                                  // 追加写入文件, 文件不存在则创建
    file, err := os.OpenFile(fileName, os.O_CREATE|os.O_APPEND|os.O_WRONLY, 0666)
    if err != nil {
        panic(Sprintln("open file failed"))
    }
    defer file.Close()

    if _, err := file.Write([]byte(s)); err != nil {
        panic(Sprintf("write file failed: %v\n", err))
    }
}
```

## OpenFile 模式

|   model    |                                        mean                                        |
| :--------: | :--------------------------------------------------------------------------------: |
| `O_RDONLY` |                 文件打开模式, 只能选 3 选 1, read only 缩写, 只读                  |
| `O_WRONLY` |                 文件打开模式, 只能选 3 选 1, write only 缩写, 只写                 |
|  `O_RDWR`  |                 文件打开模式, 只能选 3 选 1, read write 缩写, 读写                 |
| `O_APPEND` |                     文件操作模式, 可多选搭配, append, 追加写入                     |
| `O_TRUNC`  |            文件操作模式, 可多选搭配, truncate 缩写, 打开文件时清空文件             |
| `O_CREATE` |                 文件操作模式, 可多选搭配, create, 文件不存在则创建                 |
|  `O_EXCL`  | 文件操作模式, 可多选搭配, exclusive 缩写, 和 O_CREATE 模式一起使用, 文件必须不存在 |
