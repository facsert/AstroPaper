---
author: facsert
pubDatetime: 2023-03-28 15:28:43
title: Go Function
postSlug: ""
featured: false
draft: false
tags:
  - Go
description: "Go 函数"
---

<!--
 * @Author       : facsert
 * @Date         : 2023-03-28 15:28:43
 * @LastEditTime : 2023-07-28 11:39:45
 * @Description  : edit description
-->

## function 定义

```go
func <function name> (<parameter name> <parameter type>) (<return type>) {
    <functuion code>
}

func show() {                                    // 无参数, 无返回值, 对应内容不填
    Println("no parameter and no return")
}

func add(x int, y int) int {                     // 参数和类型要对应
    return x + y
}

func self(x, y int) (int, int) {                 // 同类型参数合并, 多返回值, 返回值类型需要括号
    return x, y
}

```

- func: 函数定义使用的关键字
- function name: 函数名, 同变量使用
- parameter name: 参数名, 参数名与类型成对存在
- parameter type: 参数类型, 多个同类型参数可只写 1 次
- return type: 返回值的类型
- golang 强制规定左花括号必须和 func 同一行

### 参数与返回值

golang 允许不定数量的参数
golang 允许多个返回值

```go
func sum(nums ...int) int {                      // 设置可变参数, 可变参数通常作为最后一个参数
    res := 0
    for _, i := range nums {                     // 可变参数本质是一个切片
        res += i
    }
    return res
}

Println(sum(1,2,3,4))
> 10

func calc(x, y int) (sum, sub int) {             // 指定多个返回值
    sum := x + y
    sub = x - y
    return
}

func add(x, y int) (int, int) {                  // 多个返回值时, 返回值类型需要带括号
    return x+1, y+1
}
```

## 匿名函数

golang 允许将函数赋值给变量
golang 允许函数不定义名称直接运行

```go

average := func(x, y int) int {
    return (x+y)/2
}

Println(average(6, 8))
> 7

avg := func(x, y int) int {
    return (x+y)/2
}(3,5)

Println(avg)
> 4
```

## defer

defer 关键字会将之后语句或函数放到函数结尾运行
多个 defer 语句按照先进后出的顺序执行

```go

func delay() {
    defer func() {                               // 将函数延迟到最后执行
        Println("third print")
    }()
    Println("first print")
    Println("second print")
}

> first print                                    // first second 正常按顺序打印
> second print
> third print                                    // third 延迟到最后打印

func stack() {                                   // 执行多个 defer 语句
    defer Println("4th")
    defer Println("3rd")
    defer Println("2nd")
    defer Println("1st")
}

> 1st                                            // 多个 defer 按照先进后出的顺序执行
> 2nd
> 3rd
```
