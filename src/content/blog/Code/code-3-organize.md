---
author: facsert
pubDatetime: 2022-10-21 21:20:45
title: 组织代码
slug: 组织代码
featured: false
draft: false
tags:
  - code
description: "组织代码功能"
---

<!--
 * @Author       : facsert
 * @Date         : 2022-10-21 21:20:45
 * @LastEditTime : 2023-07-28 11:46:11
 * @Description  : edit description
-->

## Table of Contents

- 模块化
- 分离工具代码
- 少写代码

## 模块化

分离相关性不大的代码, 一个模块做一件事  
模块化使得代码易于测试和扩展

## 少写代码

不要重复造轮子, 复用已有实现

```python

def unique_list(array):
    pass

def sort_list_one():
    pass

def sort_list_two():
    pass

unique_list = list(set(raw_list))                # 使用集合的特性消除重复数据(集合每个元素唯一)
sort_list = sorted(raw_list)                     # 使用库函数排序
```

## 分离工具代码

抽离与系统无关的代码

```python
def compare_list_average(list_a, list_b):

    a_sum, a_length = 0, len(list_a)
    for i in list_a:
        a_sum += i
    a_average = a_sum / a_length

    b_sum, b_length = 0, len(list_b)
    for i in list_b:
        b_sum += i
    b_average = b_sum / b_length

    return a_average > b_average


def average(array):
    array_sum, array_length = 0, len(array)
    for i in array:
        array_sum += i
    return array_sum / array_length

def compare_list_average(list_a, list_b):
    a_average = average(list_a)
    b_average = average(list_b)
    return a_average > a_average
```
