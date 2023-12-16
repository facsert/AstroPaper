---
author: facsert
pubDatetime: 2022-10-21 15:28:43
title: 代码结构
postSlug: ""
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

- 分支
- 边界
- 变量的逻辑

## 分支

- 最小化嵌套
- 正向优先

### 最小化嵌套

提前退出
优先解决简单问题

```python
if sut_os_connect():
    if sut_tool_exist():
        if sut_bmc_version():
            # flash BMC
        else:
            return 'get BMC version failed'
    else:
        return 'tool not exist'
else:
    return 'sut os connect failed'


if not sut_os_connect():                         # if sut_os_connect_fail
    return 'sut os connect failed'

if not sut_tool_exist():                         # if sut_tool_not_exist
    return 'tool not exist'

if not sut_bmc_version():                        # if sut_bmc_version_fail
    return 'get BMC version failed'

# flash BMC
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

if 5 > num:
    pass

```

## 边界

简化边界问题,
忽略或合并不关注的内容

```python

if type(bkm) == int:
    if type(bkms) == list:
        if bkm in bkms:
            return bkms.index(bkm)
        else:
            return f'{bkm} not in {bkms}'
    else:
        return f'{bkms} not a list'
else:
    return f'{bkm} type not int'


try:
    return bkms.index(bkm)
except Exception as e:
    return f'Get {bkm} index error'
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

```python
bmc_cmd = 'systemctl status docker'
cmd_ret = subprocess.Popen(bmc_cmd)
print(bmc_cmd)
print(cmd_ret)

run('systemctl status docker')
```

### 减小变量作用域

缩减变量作用域以减轻变量的追踪难度  
减小变量的使用跨度

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


# read config
config = {}
config_valid = {}

# set output file
log_dir = 'reports/process.log'

# test step
username = 'admin'
password = 'admin'
tool_dir = 'tools'
count = 0
```
