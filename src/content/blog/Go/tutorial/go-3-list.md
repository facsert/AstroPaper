---
author: facsert
pubDatetime: 2023-03-13 15:28:43
title: 03.Go List And Slice
postSlug: ""
featured: false
draft: false
tags:
  - Go
description: "Go 数组和切片"
---

<!--
 * @Author       : facsert
 * @Date         : 2023-03-13 15:28:43
 * @LastEditTime : 2023-07-31 18:04:29
 * @Description  : edit description
-->

[数组](#数组)
[切片](#切片)

## 数组

- 数组是**确定数量**元素的集合, 数组元素类型可以不一致
- 数组有容量和长度两个属性 `cap() len()` 查看数组属性
- 数组的长度和容量始终相等 `length == capacity`
- 数组元素可以修改, 但是数组长度和容量声明后就不能修改
- 数组中未赋值的元素会使用类型的默认值

```go
strList := [3]string{"hey", "you", "world"}      // 定义长度为 3 , 元素类型为字符串的数组
strList := [...]string{"hey", "you", "world"}    // 定义元素为字符串的数组, 根据值推断长度和容量

intList := [3]int{1, 2}                          // [1 2 0] 定义容量长度均为 3, 类型为 int 的数组
intList := [...]int{1, 2}                        // [1, 2]  容量和长度均为 2 的 int 数组

intArray := [3]int{1, 4}                         // [1 4 0] 初始化数组, 未定义的值取 0
intArray[1], intArray[2] = 2, 3                  // [1 2 3] 修改数组的值

len(strList)                                     // 3 strList 长度为 3
cap(intList)                                     // 3 intList 容量为 3
```

### any 类型数组

any 类型数组的元素可以是任意类型

```go
var anyList [3]any                               // [<nil> <nil> <nil>] any 类型初始值是 nil
anyList[0], anyList[1] = "he", 20000             // [he 2 <nil>]

anyList[0] = anyList[0].(string) + " llo"        // any 转实际类型操作需要显示声明
anyList[1] = anyList[1].(int) + 1

for index, value := range anyList {
    fmt.Printf("index: %v, value: %v  type: %T\n", index, value, value)
}

> index: 0, value: hello type: string
> index: 1, value: 20000 type: int
> index: 2, value: <nil> type: <nil>
```

### 数组传递

数组赋值或作为函数参数时, 传递的都是数组的拷贝  
函数若想修改源数组, 传参时需要传入数组地址

```go

func three(list [3]int) {                        // 参数是数组值传递, 外部数组不变
    list[0] = 6
}

func third(list *[3]int) {                       // 参数是数组的指针类型, 修改会同步外部数组
    list[0] = 9
}

source := [...]int{0,1,2}                       // 定义 source 数组
copy := source                                  // copy 为 source 的拷贝, 互不影响
third(&copy)                                    // 传入 copy 地址, 函数内的修改会同步给外部 copy
three(source)                                   // 传入 source 的拷贝, 函数内修改不影响外部 source
fmt.Printf("source: %v  copy: %v\n", source, copy)
> source: [0 1 2]  copy: [9 1 2]
```

## 切片

- 切片是一组**数量可变**的元素集合
- 切片是引用类型, 切片本身不存储数据, 切片赋值传递的是地址
- 切片会自动扩容以存储所有添加的数据

### 切片定义

`var` 声明切片  
`make` 构造切片  
从数组截取切片

```go
type slice struct {                              // 切片定义 $GOROOT/go/src/runtime/slice.go
    array unsafe.Pointer                         // 指向一个数组中某个元素的指针 (每个切片都对应一个数组)
    len int                                      // 切片长度
    cap int                                      // 切片容量
}

var strSlice []string                            // nil 切片声明, 创建一个 nil 切片, 与 nil 相等
intSlice := []int{}                              // [] 切片声明并初始化, 创建空切片, 与 nil 不相等
boolSlice := make([]bool, 1, 2)                  // [false] 初始化布尔切片, 长度为 1, 容量为 2

fmt.Println(strSlice == nil)                     // true nil 切片与 nil 一致
fmt.Println(intSlice == nil)                     // false 空切片与 nil 不一致
```

切片也可以从数组中截取一段  
切片的长度为截取数据的数量, 切片容量为切片开端到数组结尾数量

```go
list := [...]int{0,1,2,3,4}
slice := list[1:3]                               // [1 2] 截取数组创建切片
Printf("length: %d capacity: %d \n", len(slice), cap(slice))
> length: 2 capacity: 4                          // 切片长度为截取元素数量, 容量切片开头元素到数组结尾
```

声明的切片与从数组截取的切片完全一致, 声明的切片对应一个不可见的数组  
切片扩容超过切片容量时, 切片会指向一个更大容量的数组, 并把旧数组的数据复制到新数组

### 切片特性

切片是引用类型, 切片不存储数据, 赋值或参数时传递的是地址  
切片扩容超过容量后会生成新的切片  
切片扩容未容量不会生成新的切片

```go
intSlice := []int{0,0,0,0}                       // 整数切片初始化, 长度容量均为 4
intArray := intSlice                             // 切片赋值, 传递的是地址, 两切片指向同一个数组
intArray[0] = 1                                  // intArray 序号 0 重新赋值, intSlice 跟着变化
intArray = append(intArray, 2)                   // append 添加元素, 超出容量, 扩容返回新的切片, intArray 变更指向的数组
intArray[1] = 1                                  // intArray 序号 1 重新赋值
fmt.Printf("intArray: %v intSlice: %v\n", intArray, intSlice)
> intArray: [1 1 0 0 2] intSlice: [1 0 0 0]      // 切片扩容后, 两切片指定数组不同，值互不影响

slice := make([]int, 3, 5)                       // 构造长度为 3 容量为 5 的切片
seq := slice                                     // seq slice 指向同一组数据
seq = append(seq, 1)                             // [0 0 0 1] seq 添加数据, 未超出容量, seq 和 slice 仍指向同一组数据
slice = append(slice, 2)                         // [0 0 0 2] slice 添加数据, 未超出容量, seq 和 slice 仍指向同一组数据
Printf("seq: %v slice: %v\n", seq, slice)        // seq 和 slice 一直指向同一组数据
> seq: [0 0 0 2] slice: [0 0 0 2]                // slice 变更覆盖 seq 变更
```

Golang 1.20 切片的扩容策略 `$GOROOT/go/src/runtime/slice.go` `func growslice`

```go
newcap := old.cap
doublecap := newcap + newcap
if cap > doublecap {                             // 所需容量大于两倍的原有容量
    newcap = cap                                 // 扩容后容量等于所需容量
} else {                                         // 所需容量小于两倍原有容量情况下
    const threshold = 256                        // 设置变更策略的容量阈值
    if old.cap < threshold {
        newcap = doublecap                       // 原有容量小于 256, 新数组容量为原有两倍
    } else {
        // Check 0 < newcap to detect overflow
        // and prevent an infinite loop.
        for 0 < newcap && newcap < cap {         // 原有容量大于 256, 旧容量自增 192 加 旧容量的四分之一
            // Transition from growing 2x for small slices
            // to growing 1.25x for large slices. This formula
            // gives a smooth-ish transition between the two.
            newcap += (newcap + 3*threshold) / 4
        }
        // Set newcap to the requested cap when
        // the newcap calculation overflowed.
        if newcap <= 0 {
            newcap = cap
        }
    }
}
```

Golang 扩容增速从 2 倍逐渐减少至 1.25 倍  
不同类型切片在扩容时候还有特殊的偏移增长机制

```go
slice := make([]int, 80, 80)

for i:=0; i < 2049; i ++ {
    oldcap := cap(slice)
    slice = append(slice, 1)
    newcap := cap(slice)

    if oldcap != newcap {
        Printf("int old: %d  new %d \n", oldcap, newcap)
    }
}

> int old: 80  new 160
> int old: 160  new 336
> int old: 336  new 672
> int old: 672  new 1184
> int old: 1184  new 1696
> int old: 1696  new 2384

> int8 old: 80  new 160
> int8 old: 160  new 320
> int8 old: 320  new 640
> int8 old: 640  new 1024
> int8 old: 1024  new 1536
> int8 old: 1536  new 2304
```

### 切片操作

切片使用 `append` 添加 删除 插入元素

```go
 slice := []int{0,0,0,0}
 slice = append(slice, []int{1, 1}...)           // append 添加数据 [0 0 0 0 1 1]
 slice = append(slice[:1], slice[3:]...)         // append 删除数据 [0 0 1 1]
 slice = append(slice[:1], append([]int{2}, slice[1:]...)...)

 fmt.Printf("slice: %v\n", slice)
 > slice: slice: [0 2 0 1 1]                     // append 插入数据
```

切片索引 `0 <= index < length <= capacity`  
截取切片片段 list[low:high]  
切片索引不能为负数  
截取切片时以 list[low:high:max] 设定长度和容量

```go
list := [...]int{0,1,2,3}

list[3]                                          // 3         切片索引 从左往右 0 开始第 3 个数
list[len(list)-1]                                // 3         切片索引 最后一个数据
list[:2]                                         // [0 1]     从切片开端到索引 2 截取片段
list[1:]                                         // [1 2 3]   从索引 1 到切片结尾
list[1:3]                                        // [1 2]     切片索引扁片(左闭右开)
list[:]                                          // [0 1 2 3] 切片全部数据

list[1:2]                                        // 忽略 high [low:]    容量 3 (length - low)
list[1:2:3]                                      // 忽略 high [low:max] 容量 2 (max - low)
```

```go
slice := []int{4,1,6,2}
fmt.Println(sort.Ints(slice))                    // 切片排序
> [1 2 4 6]

copy(destSlice, sourceSlice)                    // 切片复制, dest 容量会影响复制结果
```
