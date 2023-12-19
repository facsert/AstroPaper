---
author: facsert
pubDatetime: 2023-05-23 15:28:43
title: 02.Go Base Data Type
postSlug: ""
featured: false
draft: false
tags:
  - Go
description: "Go 基本数据类型"
---

<!--
 * @Author       : facsert
 * @Date         : 2023-05-23 15:28:43
 * @LastEditTime : 2023-07-31 17:26:11
 * @Description  : edit description
-->

Golang 基本类型有

[整形](#整形)
[浮点型](#浮点型)
[布尔类型](#布尔类型)
[字符串](#字符串类型)
[类型转换](#类型转换)

## 整形

| 类型             | 范围                                       |
| :--------------- | :----------------------------------------- |
| `int8`           | -128 - 127                                 |
| `uint8` / `byte` | 0 - 255                                    |
| `int16`          | -32768 - 32767                             |
| `uint16`         | 0 - 65535                                  |
| `int32` / `rune` | -2147483648 ~ 2147483647                   |
| `uint32`         | 0 ~ 4294967295                             |
| `int64`          | -9223372036854775808 ~ 9223372036854775807 |
| `uint64`         | 0 ~ 18446744073709551615                   |
| `int`            | 32 位系统 int32， 64 位系统 int64          |
| `uint`           | 32 位系统 uint32， 64 位系统 uint64        |

### 整形类型转换

不同整形之间不可以进行运算, 需要转换为同一类型运算  
数字可以与任何整形运算, 运算结果类型不变

```go
var num8 int8 = 8
var num16 int16 = 16

Printf("type: %T\n", 3)
Printf("num8 add: %d \n",num8 + 3)               // 数字可直接与 int8 类型数据运算
Println("num16 add: %d \n",num16 + 3)
> type: int
> num8 add:11 type:int8                          // 结果任然为 int8
> num8 add:19 type:int16

Println("num16 add num8: %d \n",num16 + num16)   // int8 与 int16 不能直接运算
> ./main.go:42:34: invalid operation: num16 + num8 (mismatched types int16 and int8)
```

数字类型数据之间可以通过 `<type>()` 方式进行类型之间的转换

```go
var num int = 8                                  // int   num = 8
num8 := int8(num)                                // int8  num8 = 8
num16 := int16(num)                              // int16 num16 = 8
num32 := int32(num)                              // int32 num32 = 8
num64 := int64(num)                              // int64 num64 = 8

numFloat32 := float32(num)                       // float32 numFloat32 = 8
numFloat64 := float64(num)                       // float64 numFloat64 = 8
```

### 进制赋值

二进制 八进制 十六进制可以通过前缀赋值
二进制 八进制 十六进制可以通过不同占位符打印

```go
binary := 0b11                                   // int binary      = 3   2 进制赋值
octal := 0o11                                    // int octal       = 9   8 进制赋值
hexadecimal := 0x11                              // int hexadecimal = 17  16 进制赋值

var ten int = 10
fmt.Printf("%d \n", ten)                         // 10
fmt.Printf("%b \n", ten)                         // 1010 占位符%b表示二进制

var eight int = 077                              // 八进制  以0开头
fmt.Printf("%o \n", eight)                       // 77 占位符%o表示八进制

var sixteen int = 0xff                           // 十六进制  以0x开头
fmt.Printf("%x \n", sixteen)                     // ff 占位符%x表示十六进制
fmt.Printf("%X \n", sixteen)                     // FF 占位符%X表示十六进制
```

### 类型最大值和最小值

数字类型最大值和最小值, 值在 math 包定义

```go
package math

intSize = 32 << (^uint(0) >> 63) // 32 or 64

MaxInt    = 1<<(intSize-1) - 1
MinInt    = -1 << (intSize - 1)
MaxInt8   = 1<<7 - 1
MinInt8   = -1 << 7
MaxInt16  = 1<<15 - 1
MinInt16  = -1 << 15
MaxInt32  = 1<<31 - 1
MinInt32  = -1 << 31
MaxInt64  = 1<<63 - 1
MinInt64  = -1 << 63
MaxUint   = 1<<intSize - 1
MaxUint8  = 1<<8 - 1
MaxUint16 = 1<<16 - 1
MaxUint32 = 1<<32 - 1
MaxUint64 = 1<<64 - 1
```

## 浮点型

浮点型类型: float32 float64

```go
var float32 = 2.17
var pi float64 = 3.1415926535

Printf("e: %f \n", e)                            // e: 2.170000
Printf("pi: %f \n", pi)                          // pi: 3.141593
```

## 布尔类型

布尔值: true false  
布尔值不能进行数字运算  
布尔类型的值默认值是 false

```go
yes, no := true, false
yes == no                                        // false
yes || no                                        // true 逻辑或
yes && no                                        // false 逻辑且

love, happy := true, false                       // 布尔运算的哲学
!love                                            // false  not love is false
love == happy                                    // false  love equal to happy is false
love != happy                                    // true   love not equal to happy is true
happy && love                                    // false  love and happy is not true
happy || love                                    // true   love or happy is true
```

## 字符串类型

### 字符

byte(uint8) ACSII 表中的一个字符, 底层是一个 0-255 数字(数字与 ACSII 表字符绑定)  
rune(int32) Unicode 编码中的一个字符(包含世界大部分语言字符), Unicode 表包含 ACSII 表

```go
var a byte = 'D'                                  // 'D' 在 ACSII 中第 68 位
var c byte = 68                                   // ACSII 中 68 为 'D', 两种赋值等价

var a rune = '中'                                 // '中' 在 Unicode 中第 20013 位
var c rune = 20013                                // Unicode 中 20013 为 '中', 两种赋值等价

Printf("a equal c: %v\n", a == c)
> a equal c: true

Printf("acsii length: %d\n", len("str"))
Printf("unicode length: %d\n", len("中文"))
> acsii length: 3                                // 单个 acsii 字符长度为 1
> unicode length: 6                              // 单个 unicode 字符长度为 3, 遍历 unicode 不能用索引
```

### 字符串

Golang 字符串本质是单个字符的集合, 单个字符的本质是数字, 通过不同规范和字符对应(ACSII 和 Unicode)  
单个 acsii 和 unicode 字符长度不同, 使用 range 遍历

```go
var acsii, unicode string = "str", "中文"
for index, char := range acsii {
   Printf("Type: %T %T, value: %v, value: %c\n", char, acsii[index], char, char)
}

> Type: int32, value: 115, value: s              // range 取出的单个字符是 rune 类型
> Type: int32, value: 116, value: t
> Type: int32, value: 114, value: r
> Type: int32, value: 20013, value: 中           // 使用 range 遍历 unicode 字符串
> Type: int32, value: 25991, value: 文

for i:=0; i<len(unicode); i++ {
   char := unicode[i]
   Printf("Type: %T, value: %v, value: %c\n", char, char, char)
}

> Type: uint8, value: 228, value: ä              // 通过 index 索引取出的字符是 byte 类型
> Type: uint8, value: 184, value: ¸
> Type: uint8, value: 173, value: ­
> Type: uint8, value: 230, value: æ
> Type: uint8, value: 150, value:
> Type: uint8, value: 135, value:
```

字符串可以与单个字符组成的切片相互转化

```go
slice, list := []byte("str"), []rune("中文")
fmt.Printf("Type: %T, value: %v\n", slice, slice)
> Type: []uint8, value: []byte{0x73, 0x74, 0x72} // 字符串转 byte
> Type: []int32, value: []int32{20013, 25991}    // 字符串转 rune

var strByte byte = 'D'
var strRune rune = '文'
fmt.Printf("Type: %T, value: %v\n", string(strByte), string(strByte))
fmt.Printf("Type: %T, value: %v\n", string(strRune), string(strRune))
> Type: string, value: D                          // byte 转 string
> Type: string, value: 文                         // rune 转 string
```

unicode 数值范围内任意数字可以转化为对应的字符  
负数不在 unicode 数值范围内, 负数转化后显示一致, 未能查询到相关解释

```go
var num int = 68
var num8 int8 = 66
var num16 int16 = 66
var num32 int32 = 25991
var num64 int64 = -1

Printf("Type %T, value: %c\n", num16, byte(num16))
Printf("Type %T, value: %c\n", num32, rune(num32))

> Type int, value: D
> Type int8, value: B
> Type int16, value: B
> Type int32, value: 文
> Type int64, value: �
```

### 字符串操作

字符串定义后可以查看但不可更改

```go
str := "learning"
fmt.Printf("Type: %T value: %c base: %v\n", str[0], str[1], str[2])
> Type: uint8 value: e base: 97

str[0] = "m"
> ./main.go:37:2: cannot assign to str[0] (value of type byte)

uStr := "中文"
fmt.Printf("Type: %T value: %v length: %v\n", uStr[0], uStr[1], len(uStr))
> Type: uint8 value: 184 length: 6

strRune := []rune(uStr)
fmt.Printf("value: %#v length: %v\n", strRune, len([]rune(strRune)))
> value: []int32{20013, 25991} length: 2
```

```go
strings.Split(s, sep) []string                   // 以 sep 为分隔符, 切割字符串成切片
strings.Contains(s, sub) bool                    // 判断 s 是否包含 sub
strings.Index(s, sub) int                        // sub 在 s 的序号
strings.Join(slice, sep) string                  // 通过 sep 把 slice 连接成一个字符串
strings.Replace(s, old, new, count) string       // 将 s 中的 old 替换为 new, 替换 count 次
strings.Count(s, sub) int                        // 返回 sub 在 s 中出现次数
```

## 类型转换

任意类型数据都可以转换为字符串

```go
s := fmt.Sprinf("%v", <variable>)                // 万能转换, any -> string

s := strconv.Itoa(32)                            // 32    int -> string
s := strconv.FormatBool(true)                    // true  bool -> string
s := strconv.FormatInt(-8, 2)                    // -1000 int64 -> string(2 进制)
s := strconv.FormatUint(255, 16)                 // ff    uint64 -> string(16 进制)
```

字符串转其它类型可能会失败, 转数字时还需考虑进制和转换后的类型

```go
i, err := strconv.Atoi("-42")                    // 42   string -> int
b, err := strconv.ParseBool("true")              // true string -> bool
f, err := strconv.ParseFloat("3.14", 64)         // 3.14 string -> float64
i, err := strconv.ParseInt("-42", 10, 64)        // -42  string -> int64
u, err := strconv.ParseUint("42", 10, 32)        // 42   string -> uint32

// ParseFloat (str, bitSize)  bitSize:{32:float32, 64:float64}
// ParseInt  ParseUint (str, base, bitSize)

// str string: 待转换的字符串
// base int: str 的进制( base=16, s 为 16 进制的字符串)
// bitSize int: {0:int, 8:int8, 16:int16, 32:int32, 64:int64}
```
