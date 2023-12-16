---
author: facsert
pubDatetime: 2023-03-25 15:28:43
title: Go Branch
postSlug: ""
featured: false
draft: false
tags:
  - Go
description: "Go 条件分支"
---

<!--
 * @Author       : facsert
 * @Date         : 2023-05-23 15:28:43
 * @LastEditTime : 2023-07-28 11:39:34
 * @Description  : edit description
-->

## if else 分支

```go
 if expression {
     branch
 } else if expression {
     branch
 } else {
    branch
 }

 done := false
 if done {
    Println("done, goodbye")
 } else {
    Println("not done, continue")
 }

 if done := true; done {
    Println("done, goodbye")
 } else {
    Println("not done, continue")
 }

```

## for 循环

```go
 for initial; expression; end {
    loop
 }

 for i:= 0; i < 3; i++ {
    Printf("index: %d\n", i)
 }

 > index: 0
 > index: 1
 > index: 2
```

### for range

遍历数组, 切片, map

```go

 intSlice := []int{0, 1}
 for index, elem := range intSlice {             // 数组和切片会返回索引和值
     Printf("%d: %d\n", index, elem)
 }
 > 0: 0
 > 1: 1

 intMap := map[string]int{
    "1st": 1,
    "2nd": 2,
 }
 for key, value := range intMap {                // map 会返回 key 和 value
    Printf("%s: %d\n", key, value)
 }
 > 2nd: 2
 > 1st: 1
```

### continue

提前结束本次循环, 直接进入下一次循环

```go
 for i:=0; i < 3; i++ {
    if i == 1 {                                  // 当 i == 1， 使用 continue 提前结束
        continue                                 // 提前结束本次循环, 进入下一个循环
    }
    Println(i)
 }

 > 0
 > 2
```

### break

结束最近的一层循环, 一个 break 结束一个 for 循环

```go
 for i:=0; i < 3; i++ {
    if i == 1 {                                  // 当 i == 1， 使用 break 结束循环
        break                                    // break 只能跳出一层循环
    }
    Println(i)
 }

 > 0
```

### goto

设置 tag, 跳出复杂逻辑到 tag 语句

```go
 for i:=0; i < 3; i++ {
    for j := 0; j < 3; j++ {
        if i == 1 && j == 0 {
            Printf("loop over i:%d j:%d\n", i, j)
            goto breakTwoLoop                    // 从复杂结构中跳到 tag 对应的语句
        }
        Printf("i:%d j:%d\n", i, j)
    }
 }
 breakTwoLoop:                                   // 设置 tag 及执行语句, 提供可跳跃点
     Println("Get out of loop")

 > i:0 j:0
 > i:0 j:1
 > i:0 j:2
 > loop over i:1 j:0
 > Get out of loop
```

## switch case

```go
 switch expression {
 case branch:
     code
 case branch:
     code
 case branch:
     code
 default:
     code
 }

 switch nun := 3; num {                          // 值选择分支
 case 1,3,5,7,9:                                 // 单个分支可容纳多个值
     Println("奇数")
 case 2,4,6,8:                                   // 每个分支自带 break, 执行完分支即退出 switch
     Println("偶数")
 default:
     Println("超出范围")
 }

 > 奇数

 grade := 80
 switch  {                                       // 表达式选择分支, 变量需要在 switch 外初始化
 case grade >= 0 && grade < 60:
     Println("不及格, 好好努力")
 case grade >= 60 && grade < 80:
     Println("不错的成绩, 继续加油!")
 case grade >= 80 && grade <= 100:
     Println("很棒哦!")
 default:
     Println("分数错误")
 }

 > 很棒哦!
```
