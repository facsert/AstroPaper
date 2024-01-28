---
author: facsert
pubDatetime: 2023-10-08 21:52:12
modDatetime: 
title: 06.Bash List
slug: 06.Bash List
featured: false
draft: false
tags:
  - bash
description: "Bash 列表"
---

<!--
 * @Author: facsert
 * @Date: 2023-10-08 21:52:12
 * @LastEditTime: 2023-10-08 22:36:51
 * @LastEditors: facsert
 * @Description:
-->

## Table of Contents

## 数组

使用 `declare` 关键字或者 `=` 创建数组

```bash
 $ declare -a array=("array" "array" "array")    # 使用 declare 关键字创建数组
 $ list=("list" "list" )                         # 等号创建数组

 $ echo "list:${list[@]}  array:${array[@]}"     # 打印数组全部数据
 > list:list list  array:array array array

 $ lst=()                                        # 创建空数组
 $ lst[1]="1st"; lst[2]="2nd"; lst[4]="4th"      # 向数组中添加数据, 从 0 开发, 可以跳过
 $ echo ${lst[@]}
 > 1st 2nd 4th                                   # 只打印赋值数据
```

数组属性: 数组长度, 数组下标, 子数组

```bash
 $ array=([1]=1st [2]=2nd [4]=4th)               # 创建数组, 并赋值
 $ echo ${array[@]}
 > 1st 2nd 4th

 $ echo ${#array[@]}                             # 数组的长度
 > 3

 $ echo ${!array[@]}                             # 打印数组下标
 > 1 2 4

 $ echo ${array[@]:0:3}                          # 获取数组子数组, 从序号 0 开始, 取 3 个
 > 1st 2nd                                       # 数组值 n 1st 2nd n 4th; 0 ,3未赋值, 从 0 后开始取 3 个有效元素

 $ list=(5 6)
 $ array+=${list[@]}                             # 数组追加
 $ echo ${array[@]}
 > 5 6 1st 2nd 4th

 $ unset array[2]                                # 删除数组元素
 $ echo ${array[@]}
 > 1st 4th
```

## 关联数组

```bash
 $ declare -A map                                # 创建关联数组
 $ map["name"]="petter"                          # 赋值
 $ map["age"]="18"                               # 赋值
 $ echo "map:${map[@]} name:${map[name]}"        # 打印关联数组
 > map:18 petter name:petter
```
