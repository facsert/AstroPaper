---
author: facsert
pubDatetime: 2023-03-27 15:28:43
title: 06.Go Struct
slug: 06.Go Struct
featured: false
draft: false
tags:
  - Go
description: "Go 结构体"
---

<!--
 * @Author       : facsert
 * @Date         : 2023-03-27 15:28:43
 * @LastEditTime : 2023-07-28 11:39:56
 * @Description  : edit description
-->

## Table of Contents

## Struct

### struct 初始化

```go
 type <struct name> struct {                     // 定义结构体, 结构体可以认为是自定义的数据类型
   <attribute name>  <type>                      // 定义结构体属性及其类型
   <attribute name>  <type>                      // 每个属性名唯一, 不能重复
   ...
 }

 type Student struct {                           // 定义 Student 类型数据
   name, city  string                            // Student 类型有 name city 属性, 属性值为 string
   age int                                       // Student 类型有 age 属性, 属性类型为 int
 }

 var kertory Student                             // 初始化 Student 类型变量 kertory
 kertory.name = "kertory"                        // 使用 . 赋值
 kertory.age = 18

 facsert := Student{                             // 初始化 Student 类型变量 facsert
   name: "facsert",
   city: "shanghai",
 }

 Printf("%#v\n", kertory)
 Printf("%#v\n", facsert)

 > main.Student{name:"kertory", city:"", age:18} // 未初始化的属性使用类型的零值
 > main.Student{name:"facsert", city:"shanghai", age:0}
```

### struct 方法

```go
 type Student struct {
   name, city string
   age int
 }

 func (s Student) Learn() {
   Printf("%s learnig in %s\n", s.name, s.city)
 }

 func (s Student) Play() {
   Printf("%s play games\n", s.name)
 }

 facsert := Student{
    name: "facsert",
    city: "shanghai",
 }

 facsert.Learn()
 facsert.Play()

 > facsert learnig in shanghai
 > facsert play games
```

### struct 属性修改

```go
 type Student struct {
   name, city string
   age int
 }

 func (s Student) rename1(name string) {         // 重命名属性 name
   s.name = name
   Printf("rename1 to %s\n", s.name)
 }

 func (s *Student) rename2(name string) {        // 重命名属性 name
   s.name = name
   Printf("rename2 to %s\n", s.name)
 }

 facsert := Student{
    name: "facsert",
    city: "shanghai",
 }

 facsert.rename1("kertory")
 facsert.rename2("squtary")
 Printf("name: %s\n", facsert.name)

 > rename1 to kertory                            // 重命名结果只在函数内生效
 > rename2 to squtary                            // 重命名结果对结构体生效
 > name: squtary
```

### struct 转 json

```go
import (
   . "fmt"
   "encoding/json"
)

 type Student struct {
     Name string                                 // 属性名大写其它模块可访问, 可以转 json
     city string                                 // 属性名小写其它模块不能访问也不能转 json
     Age int     `json:"age"`                    // 可以通过定义 tag, 修改转化为 json 后 key 名称
}

 facsert := Student{
    Name: "facsert",
    city: "shanghai",
    Age: 18,
 }

 jsonStu, err := json.Marshal(facsert)           // struct 转 json
 if err != nil {
   panic("json mashal failed")
 }

 Println(facsert)
 Println(string(jsonStu))

 > {facsert shanghai 18}                         // facsert 结构体所有属性值
 > {"Name":"facsert","age":18}                   // city 属性不在, Age 属性名变为 age
```
