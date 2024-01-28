---
author: facsert
pubDatetime: 2022-09-27 15:28:43
title: Go Pointer
slug: Go Pointer
featured: false
draft: false
tags:
  - Go
description: "Go 指针"
---

<!--
 * @Author       : facsert
 * @Date         : 2022-09-27 15:28:43
 * @LastEditTime : 2023-10-11 22:11:14
 * @Description  : edit description
-->

## Table of Contents

## 指针

- 变量
- 指针变量

|   类型   |  变量名   |  变量值  | 变量存储地址 |
| :------: | :-------: | :------: | :----------: |
|   变量   |   name    |   100    |   0x100001   |
| 指针变量 | name_addr | 0x100111 |   0x100002   |

```Go
 strVar := "initial"                             // 初始化变量赋值, 绑定一个固定地址, 值可以变, 地址不变
 Printf("initial strVar value:%v, addr: %p\n", strVar, &strVar)

 straddr := &strVar                              // 初始化地址类型变量并赋值, 变量绑定地址, 变量值是一个地址
 Printf("initial straddr value: %v, addr: %p\n", straddr, &straddr)

 addrVal := *straddr                             // 初始化并赋值, 变量绑定地址, 地址类型才能取值
 Printf("initial strVar value:%v, addr: %p\n", addrVal, &addrVal)

 strVar = "modify"

 Printf("modify strVar value:%v, addr: %p\n", strVar, &strVar)
 Printf("modify straddr value: %v, addr: %p\n", straddr, &straddr)
 Printf("modify strVar value:%v, addr: %p\n", addrVal, &addrVal)

 > strVar value:initial, addr: 0xc0000142b0
 > straddr value: 0xc0000142b0, addr: 0xc000012038
 > addrVal value:initial, addr: 0xc0000142d0

 > strVar value:modify, addr: 0xc0000142b0       // strVar 重新赋值(地址对应的值变化), 地址不变
 > straddr value: 0xc0000142b0, addr: 0xc000012038
 > addrVal value:initial, addr: 0xc0000142d0     // 地址对应的值没有变化

```

定义变量后, 该变量的地址不变, 地址内的值可变化

### 变量地址

- 符号: & 获取变量的地址
- 符号: \* 指针变量的值(地址)取值

```Go
 var str string = "hello"                        // 定义一个字符串变量 str, 值是 "hello" 地址是 0xc00001a078

 addr := &str                                    // addr 类型为 *string(地址类型), addr 的值是 0xc00001a078(str 地址), addr 地址是 0xc00000e018

 tmp := *addr                                    // 根据 addr 的值(0xc00001a078 str 地址)取值到 "hello" 赋值给 tmp. 等同于 tmp := "hello"

 *addr = "end"                                   // str 的地址不变, addr 指针一直指向 str 的值, 与 str = "end" 效果一致
```
