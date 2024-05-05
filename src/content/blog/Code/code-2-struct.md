---
author: facsert
pubDatetime: 2022-10-21 15:28:43
title: 代码结构
slug: 代码结构
featured: false
draft: false
tags:
  - code
description: "简化代码控制流"
---

<!--
 * @Author       : facsert
 * @Date         : 2022-10-21 15:28:43
 * @LastEditTime : 2023-07-28 11:46:04
 * @Description  : edit description
-->

## Table of Contents

## 分支

- 最小化嵌套
- 正向优先

### 最小化嵌套

提前退出, 优先解决简单问题

```python
if connect_client():
    if file_exist():
        if read_file():
            # read file success
        else:
            return 'read file failed'
    else:
        return 'file not exist'
else:
    return 'connect host failed'


if not connect_client():
    return 'connect host failed'

if not file_exist():
    return 'file not exist'

if not read_file():
    return 'read file failed'

# read file success
```

### 正向优先

正向结果优先

```python
if is_pass:
    # case PASS ...
else:
    # case FAIL ...

if not is_pass:
    # case FAIL ...
else:
    # case PASS ...

if is_fail:
    # case FAIL ...
else:
    # case PASS ...

```

分支参数, 左边为变量, 右边为常量

```python
 if num > 5:
    pass
```

## 边界

简化边界问题, 忽略或合并不关注的内容

```python

array = [1, 2, 3, 4, 5]

if type(index) is int:
    if index >= (-1 * len(array)):
        if index < len(array):
            return array[index]
        else:
            return f"{index} bigger than array length"
    else:
        return f'{index} less than 0'
else:
    return f'{index} is not int'

# 合并 index error 的多种情况
try:
    return array[index]
except IndexError as e:
    return f'{index} error: {e}'
```

## 变量的逻辑

- 少创建无效变量
- 减小变量作用域

### 少创建无效变量

变量越多负担越重  
消除无效的中间变量

```python

note = 'Continue the test when error occurred? (default: Y  N/Y)'
input_raw = input(note)
input_no_space = input_raw.strip()
input_up = input_no_space.upper()
input_ok = input_up in ('Y', 'N', '')

if input_up in ('Y', ''):
    print('continue test when error occurred')
else:
    print('stop test when error occurred')


note = 'Continue the test when error occurred? (default: Y  N/Y)'
if input(note).strip().upper() in ('', 'Y'):
    print('continue test when error occurred')
else:
    print('stop test when error occurred')
```

### 减小变量作用域

缩减变量作用域以减轻变量的追踪难度  
减小变量的使用跨度, 变量定义尽量与使用位置接近

```python
username = 'admin'
password = 'admin'
config = {}
config_valid = {}
tool_dir = 'tools'
log_dir = 'reports/process.log'
count = 0
key = None
value = None


# read config module
config = {}
config_valid = {}

# set output module
log_dir = 'reports/process.log'

# client module
username = 'admin'
password = 'admin'
tool_dir = 'tools'
count = 0
```
