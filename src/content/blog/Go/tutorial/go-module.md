---
author: facsert
pubDatetime: 2023-07-31 17:02:03
title: Go Module
slug: Go Module
featured: false
draft: false
tags:
  - Go
description: "Go 模块"
---

<!--
 * @Author       : facsert
 * @Date         : 2023-07-31 17:02:03
 * @LastEditTime : 2023-07-31 17:02:40
 * @Description  : edit description
-->

## Table of Contents

使用 `import` 关键字引入官方库, 自定义包或第三方库

## 官方标准库

```go

import "fmt"
import "strings"

import (
    "fmt"
    "strings"
)

func main() {
    fmt.Println("fmt package")
    strings.Replace("fmt package", "fmt", "strings", 1)
}
```

- 未被使用模块会报未引入错误: `imported and not used: "strings"`
- 可以使用 `_` 符号避免未使用模块报错
- 使用 `.` 符号, 使用模块方法时可以不用带模块名称
- 使用 `<name>`，给模块添加别名
- 可被外都调用包方法开头字母都是大写

```go
import (
    . "fmt"                                      // 使用模块时, 可以省略模块名称
    str "strings"                                // 定义模块别名
    _ "os"                                       // 只声明而不导入模块
)

func main() {
    Println("fmt package")                       // 省略模块名称 "fmt"
    str.Replace("fmt package", "fmt", "strings", 1)
}
```

### 第三方库

```bash
 $ go get -u <module>

 $ ls -l
 > -rw-r--r-- 1 root root  2031 Jul 26 23:32 go.mod
 > -rw-r--r-- 1 root root 16603 Jul 26 23:32 go.sum
 > -rw-r--r-- 1 root root   935 Jul 26 23:21 main.go
```

第三方库下载到本地后, 引入方式与标准库一致

```go
import {
    "fmt"                                        // 标准库在前
    "strings"

    "github.com/gin-gonic/gin"                   // 第三方库在后
    "github.com/swaggo/files"
}
```

### 自定义模块

使用 go mod 创建项目, 创建多级目录存放自定义模块

```bash
demo                                             # 创建 demo 文件夹, 存放项目
├── go.mod                                       # module learn  使用 go mod 在 demo 文件下创建 learn 项目
├── main.go                                      # package main  learn 项目的入口
└── lib
    ├── config                                   # 创建 config 文件夹存放 config 模块, 文件夹最好与模块同名, 以便于查询
    |   ├── remote.go                            # remote.go 属于 config 模块, 是 config 模块进一步划分
    |   └── local.go                             # local.go 属于 config 模块, 是 config 模块进一步划分
    └── summary                                  # 创建 summary 文件夹存放 log 模块, 反面示例. 模块与文件夹不同名
        └── logger.go                            # logger.go 属于 log 模块
```

- 单个文件夹只允许存放一个包, 包内可以继续划分为多个文件
- 引入包时, 只需要引入包的目录即可, 无需指定文件
- 包名可以与模块名称不一致, 但是在引入后难以通过引入语句路径查找模块位置

`local.go` 添加代码

```go
package config

import "fmt"

func Head() {
    fmt.Println("func Head config module")
}

func head() {
    fmt.Println("func head config module")
}
```

`logger.go` 添加代码

```go
package log

import "fmt"

func Display() {
    fmt.Println("func Display log module")
}
```

`main.go` 引入 `config` 和 `log` 模块的方法

```go
import {
    "fmt"
    "learn/lib/config"                           // 引入 config 模块, config 模块在 lib/config 目录下, 模块与文件夹同名
    "learn/lib/summary"                          // 引入 log 模块, log 模块在 lib/summary 目录下, 模块与文件夹不同名
}

func main() {
    fmt.Println("import config")
    config.Head()                                // 使用 config 包内的函数
    log.Display()                                // 使用 log 包内函数
}
```
