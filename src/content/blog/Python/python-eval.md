---
author: facsert
pubDatetime: 2022-08-30 12:08:37
title: Python eval
postSlug: ""
featured: true
draft: false
tags:
  - Python
description: "Python 标准函数 eval exec"
---

<!--
 * @Author       : facsert
 * @Date         : 2022-08-30 12:08:37
 * @LastEditTime : 2023-09-27 21:04:25
 * @Description  : edit description
-->

## eval

eval 能评估字符串转换为表达式, 返回表达式的值

```python
 eval(string[, globals: dict[, locals:dict]])    # 后两个参数类型为字典
 eval(string, None, None)                        # 后两个默认参数, 默认值为 None

 a, b = 1, 2
 eval('a + b')                                   # 将字符串转换成表达式
 > 3                                             # 返回表达式的执行结果

 variable = -2
 eval('abs(variable)')                           # 使用内置函数
 > 2
```

- 限定范围

```python
 eval(string, None, local:dict)                  # 所有全局变量可用, 局部变量限定字典内
 eval(string, global:dict, None)                 # 所有局内变量可用, 全局变量限定字典内

 glo_var = 'global variable'
 def eval_test():
    loc_val = 'local variable'
    eval('print(glo_val + loc_val)', {}, None)   # 全局变量受限, 无法识别 glo_var
 > name 'glo_val' is not defined

 glo_var = 'global variable'
 def eval_test():
    loc_val = 'local variable'
    eval('print(glo_val + loc_val)', None, {})   # 局内变量受限, 无法识别 loc_val
 > name 'loc_val' is not defined

 glo_var = 'global variable'
 def eval_test():
    loc_val = 'local variable'
    eval('print(glo_val + loc_val)', {}, {})     # 局内变量, 全局变量均受限
 > name 'loc_val' is not defined

 glo_var = 'global variable'
 def eval_test():
    loc_val = 'local variable'
    eval('print(glo_val + loc_val)', None, None) # 变量取值不受限制, 正常取值
 > name 'loc_val' is not defined
```

- 安全性

全局变量存在一个内置命名空间, 包含很多常用函数和属性(无需导入即可使用)

```python

 eval('abs(a)', {a: -3}, {})                     # 全局变量和局内变量只提供一个变量, 还是能识别 abs 函数
 > 3

 eval(string, {'__builtins__': None})

 eval('abs(-3)', {'__builtins__': None}, None)   # global 隐式携带内置命名空间, 必须显式声明, {} 无效
 > 'NoneType' object is not subscriptable

```

注: 避免将未知字符转换为代码, 需要屏蔽 python 内置函数和属性

## exec

exec 将字符串转换为代码块, 返回值为 None

```python
 exec(string[, globals: dict[, locals:dict]])    # 后两个参数类型为字典
 exec(string, None, None)                        # 后两个默认参数, 默认值为 None

 exec('a = 2 + 3')                               # 与正常执行语句一致
 print(a)
 > 3

 eval('a = 2 + 3')                               # eval 无法执行语句
         ^
 SyntaxError: invalid syntax


 a = exec('2 + 3')                               # exec 也可执行表达式, 但返回值为 None
 print(a)
 > None

 a = eval('2 + 3')                               # eval 能正常返回表达式结果
 print(a)
 > 5
```
