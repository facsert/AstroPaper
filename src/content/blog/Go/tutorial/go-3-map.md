---
author: facsert
pubDatetime: 2023-03-23 15:28:43
title: Go Map
postSlug: ""
featured: false
draft: false
tags:
  - Go
description: "Go Map 类型数据"
---

<!--
 * @Author       : facsert
 * @Date         : 2023-03-23 15:28:43
 * @LastEditTime : 2023-09-07 22:22:44
 * @Description  : edit description
-->

## Map

map 是一组**无序**的键值对的集合  
map 是引用类型, 赋值时传递的是地址

### map 初始化

map 只声明不初始化取零值 nil, nil map 只能读不能写入  
map 初始化未添加值是 empty map, empty map 不等于 nil map

```go
map[<key type>]<value type>                      // map 类型

var strMap = make(map[string]string, 1)          // 声明并初始化一个 empty map, map 容量为 2
strMap["a"] = "a"                                // 已初始化的 map 赋值
strMap["b"] = "b"

intMap := map[string]int {                       // intMap 声明并初始化, 并赋值
   "one": 1,
   "two": 2,
}

Printf("int map %#v\n", intMap)
Printf("str map %#v\n", strMap)
Printf("str map length %v\n", len(strMap))

> int map map[string]int{"one":1, "two":2}
> str map map[string]string{"a":"a", "b":"b"}    // map 可添加数据, 允许超过容量
> str map length 3                               // 无法使用 cap 查看 map 容量, 可以使用 len 查看长度
```

### map 传递

map 将引用拷贝了一份给赋值变量, 两个引用指向同一个数据  
map 作为函数参数传递时也是拷贝一份引用进入函数

```go
intMap := map[string]int {
   "1st": 1,
}

copyMap := intMap                                // map 是引用类型, 传递的是引用并非数据的拷贝
copyMap["2nd"] = 2
intMap["1st"] = 4

Printf("intMap %#v\n", intMap)
Printf("copyMap %#v\n", copyMap)

> intMap map[string]int{"1st":4, "2nd":2}        // 任意一个变量修改数据, 两个变量都会同步修改
> copyMap map[string]int{"1st":4, "2nd":2}       // 两个变量指向同一个值, 结果一致
```

### map 遍历

map 使用 range 遍历 map  
map 值拷贝也需要使用 range 遍历, copy 只适用于切片

```go
intMap := map[string]int {
   "1st": 1,
   "2nd": 2,
   "3rd": 3,
}

copyMap := make(map[string]int, 3)

for key, value := range intMap {
   copyMap[key] = value
   Printf("%v: %v \n", key, value)
}

> 1st: 1
> 2nd: 2
> 3rd: 3
```

### map 取值

map 使用 [key] 取值

```go
 intMap := map[string]int {
    "1st": 1,
    "2nd": 2,
 }

 value, ok := intMap["3rd"]                      // map 取值

 if ok == true {                                 // ok 为 true, vaule 为对应 key 的值
    Printf("value: %v\n", value)
 }

 if ok == false {                                // ok 为 false, map 不存在 key, value 为类型零值
    Printf("key not in map")
 }
```

### map 删除

```go
 intMap := map[string]int{
    "1st": 1,
    "2nd": 2,
 }

 delete(intMap, "1st")
```
