---
author: facsert
pubDatetime: 2022-10-21 21:20:45
title: 组织代码
postSlug: ""
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

# 拓展

## 函数式编程

简单说，"函数式编程"是一种"编程范式"（programming paradigm），也就是如何编写程序的方法论
它属于"结构化编程"的一种，主要思想是把运算过程尽量写成一系列嵌套的函数调用

### 只用表达式不用语句

每一步都是计算, 都有返回值

### 无副作用, 不改变外部变量

不改变外部变量或状态

### 纯函数, 参数与返回值一一对应

相同输入输出必定相同

## SOLID 原则

SOLID 是面向对象编程应当遵守的准则和最佳的实践方向

### SRP 单一职责

> Single Responsibility Principle
> There should never be more than one reason for a class to change

### OCP 开闭原则

原始定义:

> Open Closed Principle

### LSP 里式替换原则

> Liskov Substitution Principle

### ISP 接口替换原则

> Interface Segregation Principle

### DIP 依赖倒置原则

> Dependency Inversion Principle
