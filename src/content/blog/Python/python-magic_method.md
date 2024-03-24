---
author: facsert
pubDatetime: 2023-10-25 11:56:31
title: Python Magic Method
slug: Python Magic Method
featured: false
draft: false
tags:
  - Python
description: "Python 魔术方法"
---

<!--
 * @Author       : facsert
 * @Date         : 2023-10-25 11:56:31
 * @LastEditTime : 2023-10-25 15:16:58
 * @Description  : edit description
-->

## Table of Contents

## 魔术方法

魔术方法是 Python 对象中特殊方法, 方法开头和结尾, 例如 `__init__`  
python 对象的大多数操作都是通过魔术方法定义的  
魔术方法大都由对应的标准函数触发

## **new** and **init**

`__new__` : 在对象实例化前时候调用, 可自定义返回值  
`__init__`: 在对象创建之后调用, 用来初始化对象属性, 返回值必须是 None

```python
class Person:

    def __new__(self, *args, **kwargs):
        print('before create object')
        return object.__new__(self)

    def __init__(self, name):
        print(f'after create object, init object {name}')

Person('petter')
before create object
after create object, init object petter


class Person:

    def __new__(self, *args, **kwargs):
        print('before create object')
        return object.__new__(self)

    def __init__(self, name):
        print(f'after create object, init object {name}')

Person('petter')
before create object
```

注意: 若 `__new__` 未返回一个实例化对象，则 `__init__` 不执行

```py
class Person:

    def __new__(cls, name, age):
        print('class init')
        cls.name, cls.age = name, age
        return cls

p = Person(name='lily', age=16)
print(f'class {Person.name} {Person.age}')
print(f'class {p.name} {p.age}')

class init
class lily 16
class lily 16
```

通过魔术方法 new, 使类初始化达到对象初始化的效果

## **repr** **str**

`__repr__` : 返回对象的可打印字符串, 用于调试  
`__str__` : 返回对象的可打印字符串, 用于用户

```py
class Person:

    def __repr__(self):
        return "repr content"

    def __str__(self):
        return "str content"

p = Person()
print(p)                                         # str content
print(repr(p))                                   # repr content
```

print / str / repr 调用顺序
repr => Person.**repr**() > object.**repr**()
print / str => Person.**str**() > Person.**repr**() > object.**repr**()

## **file** **function** **name**

`__file__` : 返回当前模块所在的文件绝对路径  
`__function__` : 返回当前函数的名称
`__name__` : 返回当前模块的名称(若文件为 python 入口文件, 则为 **main**)

```py
# package.py 写入代码
print(f"package.py {__file__} {__name__}")
name = "John"

# run.py 写入代码
if __name__ == '__main__':
    print(f"run.py {__file__} {__name__}")

    from package import name
    print(f"import {name = }")

# python run.py 执行 run.py
run.py E:\文档\Python\run.py __main__
package.py E:\文档\Python\package.py package
import name = 'John'
```

## **iter** **next**

`__iter__` : 返回一个迭代器对象, 用于迭代对象  
`__next__` : 返回迭代器对象的下一个值, 用于迭代对象

```py

```

## **getitem** **setitem**

`__getitem__` : 返回对象的某个元素  
`__setitem__` : 设置对象的某个元素

```py

```

## **enter** **exit**
