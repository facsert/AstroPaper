---
author: facsert
pubDatetime: 2023-10-09 20:03:44
title: Bash Function
postSlug: ""
featured: false
draft: false
tags:
  - bash
description: "Bash 函数"
---

<!--
 * @Author: facsert
 * @Date: 2023-10-09 20:03:44
 * @LastEditTime: 2023-10-09 20:42:30
 * @LastEditors: facsert
 * @Description:
-->

## 函数定义

```bash
functionNamr() {
    code
}

function functionName() {
    code
}

function functionName {
    code
}
```

## 函数调用

```bash
function hello() {                               # 定义函数
    local name=$1                                # 获取第一个参数的值
    echo "Function hello $name"
}

hello "lily"                                     # 函数调用

Function hello lily                              # 函数执行结果
```

## 函数返回

```bash
function max() {                               # 定义函数
    local num1=$1
    local num2=$2
    [[ $num1 -gt $num2 ]] && return $num1
    return $num2
}

max 7 3                                          # 函数调用
echo "max: $?"                                   # 获取函数返回值
```

## 注意

函数内变量使用局域变量, 避免使用全局变量(同名覆盖)

```bash
function test() {
    local name=$1                                # 声明局域变量, 仅函数内生效
    index=$2                                     # 全局变量, 函数外可用
}

test "lily" 1                                    # 函数调用
echo "name:$name index:$index"                   # 打印函数内变量

name: index:1                                    # 局域变量在函数外不可用, 全局变量在外可用
```
