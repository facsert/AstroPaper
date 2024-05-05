---
author: facsert
pubDatetime: 2023-10-09 20:03:44
modDatetime:
title: 04.Bash Function
slug: 04.Bash Function
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

## Table of Contents

## 函数定义

```bash
functionName() {
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

$ hello "lily"                                   # 函数调用
> Function hello lily                            # 函数执行结果
```

函数允许使用参数, 参数使用 `$1`、`$2`、`$3` 等获取  
`$0` 表示函数名  
`$#` 表示参数个数  
`$*` 表示所有参数  
`$@` 表示所有参数

```bash
function func() {                               # 在 test.sh 中定义函数
    for param in $*; do
        echo "param $param"
    done
    echo "param length: $#"
    echo "full param: $@"
}
func 1st 2nd 3rd                                 # 函数调用

 $ bash test.sh                                  # 执行脚本
 > param 1st
 > param 2nd
 > param 3rd
 > param length: 3
 > full param: 1st 2nd 3rd
```

## 函数返回

使用 `return` 返回函数值, 返回值只能是数字  
一般使用 `0` 表示执行成功, `1` 获取其他数字表示执行失败

```bash
function bigger() {                              # 定义函数
    [[ $1 -gt $2 ]] && return 0 || return 1      # 返回值只允许数字
}

$ num1=7; num2=3; bigger num1 num2               # 变量定义, 函数调用
$ [[ $? -eq 0 ]] && echo "$num1 bigger" || echo "$num2 bigger"
> 7 bigger
```

## 变量作用域

定义的变量默认为全局变量, 使用 `local` 定义为局部变量
为避免使用全局变量污染(同名覆盖), 函数内变量使用局域变量

```bash
count=0                                          # 定义全局变量 count
function func() {
    local index=0                                # 定义局内变量 index
    for param in $*; do
        count=$((count + 1))
        index=$(expr $index + 1)
    done
    echo "local count: $count index: $index"     # 函数内打印
}

func 1st 2nd 3rd                                 # 函数调用
echo "global count: $count index: $index"        # 函数调用
echo "param: $param"

 $ bash test.sh                                  # 执行脚本
 > local count: 3 index: 3                       # 函数内部全局变量和局部变量都可用
 > global count: 3 index:                        # 函数外局部变量不可用
 > param: 3rd                                    # 函数内部变量污染全局变量
```
