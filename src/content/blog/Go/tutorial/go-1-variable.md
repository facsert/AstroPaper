---
author: facsert
pubDatetime: 2022-07-19 19:21:13
title: Go variable
postSlug: ""
featured: false
draft: false
tags:
  - Go
description: "Go 变量"
---

<!--
 * @Author       : facsert
 * @Date         : 2022-07-19 19:21:13
 * @LastEditTime : 2023-07-31 17:13:17
 * @Description  : edit description
-->

- [变量](#变量)
- [常量](#常量)
- [打印](#打印)
- [附录](#附录)

## 变量

- Go 变量命名要求以字母或下划线开头
- 不可以使用 Go 中的 25 [关键字](#关键字)及 37 个[保留字](#保留字)
- 变量要求声明类型或使用类型推导

### 变量声明

```go
var <variable name> <type> = <value>             // 使用 var 进行变量定义, 最完整的变量定义
var age int = 18                                 // 声明变量类型并赋值

var <variable name> <type>                       // 变量定义, 只定义变量名和类型, 变量默认为对应类型的初始值
var str string                                   // 声明变量和类型, 未赋值使用默认值(string 默认值 "")

var <type> = <value>                             // 变量定义, 通过值自动推导变量类型
var age = 18                                     // 声明变量, 赋值, 并使用类型推导

<variable name> := <value>                       // 变量定义, 通过值自动推导类型, 仅能在函数内部使用
name := "str"                                    // 声明变量, 赋值, 并使用类型推导(仅限于函数内使用)

var (
   str    string                                 // ""    声明变量未赋值, 使用 string 类型默认值 ""
   num    int                                    // 0     声明变量未赋值, 使用 int 类型默认值 0
   boolen bool                                   // false 声明变量未赋值, 使用 bool 类型默认值 false
)

var (
   str string = "hello"                          // 批量声明并赋值
   num int    = 4
   boolen     = true
)

str, num := "hello", 3                           // 多个变量声明并赋值
```

### 匿名变量

Go 中存在**未被使用**的变量会报错  
使用匿名变量 "\_" 处理无需使用的变量

```go
var name string = "facsert"
fmt.Println("hello world")
> ./main.go:10:6: name declared but not used     // 变量 name 未使用, 报错

name, _ := "facsert", "variable"                 // 使用匿名变量, 忽略后一个值
fmt.Println("hello ", name)
> hello  facsert

import {
   . "fmt"                                       // 使用模块时无需添加模块名称
   _ "os"                                        // 引入但未使用的模块, _ 处理后不会报错
}

Println("hello world")                           // 引入时使用 . 使调用 fmt 方法无需添加模块名称
```

## 常量

常量声明时候必须赋值  
常量一旦声明就不可变化

```go
const e = 2.7182                                 // 常量声明必须要赋值, 且不可更改

const (                                          // 快速声明常量, a b 赋值 10
   a = 10                                        // a = 10
   b                                             // a = 10 继承上一个变量的值
   c = 5                                         // c = 5
   d                                             // d = 5
)
```

### iota

iota 是定义常量时使用的自增关键字  
同 const 定义块互不干扰, 所有注释行和空行全部忽略  
从第 1 行开始，iota 从 0 逐行加 1

```go
const (
   a = iota                                      // iota = 0  a = 0
   b                                             // iota = 1  b = 1
   c                                             // iota = 2  c = 2
)

const (                                          // 变更初始值
   a = iota + 3                                  // iota = 0  a = 3
   b                                             // iota = 1  b = 4
   c = iota + 2                                  // iota = 2  c = 4
   d                                             // iota = 3  d = 5
)

const (                                          // iota 插值
   a = iota                                      // iota = 0  a = 0
   b = 5                                         // iota = 1  b = 5
   c = iota                                      // iota = 2  c = 2
   d                                             // iota = 3  d = 3
)
```

## 打印

- 通过占位符替换值打印
- 通过不同占位符可以打印变量不同属性值
- 万能占用符 `%v` (`%#v %+v` 表示不同打印形式)

```go
fmt.Printf(<str>, <string>, <int>, <bool> ...)   // 第一个参数是字符串,后接任意类型值, 占位符需与值数量对应

fmt.Printf("common: %v \n", "common")            // 万能占用符, 自动判断类型
fmt.Printf("string: %s \n", "string")            // 字符串变量占位符
fmt.Printf("char  : %c \n", 'c')                 // 单个字符变量占位符, 单个字符用单引号
fmt.Printf("number: %d \n", 4)                   // 整形变量占位符
fmt.Printf("float : %f \n", 2.33)                // 浮点型变量占位符
fmt.Printf("bool  : %t \n", true)                // 布尔值变量占位符
fmt.Printf("point : %p \n", *variable)           // 指针变量占位符

fmt.Printf("variable type: %T \n", variable)     // 变量类型占位符
```

## 附录

Golang 中存在一些关键字和保留字， 不能作为变量名称

### 关键字

```go
   break        default      func         interface    select
   case         defer        go           map          struct
   chan         else         goto         package      switch
   const        fallthrough  if           range        type
   continue     for          import       retrun       var
```

### 保留字

```go
    Constants:    true  false  iota  nil

        Types:    int  int8  int16  int32  int64
                  uint  uint8  uint16  uint32  uint64  uintptr
                  float32  float64  complex128  complex64
                  bool  byte  rune  string  error

    Functions:   make  len  cap  new  append  copy  close  delete
                 complex  real  imag
                 panic  recover
```
