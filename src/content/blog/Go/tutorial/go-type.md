---
author: facsert
pubDatetime: 2023-07-10 14:24:30
title: Go Generics
postSlug: ""
featured: false
draft: false
tags:
  - Go
description: "Go 泛型"
---

<!--
 * @Author       : facsert
 * @Date         : 2023-07-10 14:24:30
 * @LastEditTime : 2023-11-27 22:16:03
 * @Description  : edit description
-->

## 类型约束

```go
func detail[T string| int| bool](item T) {       // 多类型参数定义
    fmt.Printf("value: %v \n", variable)         // 打印变量的值
    fmt.Printf("type: %T", variable)             // 打印变量的类型
}

func add[T string| int](a T, b T) {              // 多类型参数定义, a b 类型相同, 且都为 string 或 int
    fmt.Println(a + b)                           // 打印结果
}
```

## comparable and ordered

comparable(可比较) 和 可排序(ordered)

官方定义

```go
type Ordered interface {
    ~int | ~int8 | ~int16 | ~int32 | ~int64 |
        ~uint | ~uint8 | ~uint16 | ~uint32 | ~uint64 | ~uintptr |
        ~float32 | ~float64 |
        ~string
}
```
