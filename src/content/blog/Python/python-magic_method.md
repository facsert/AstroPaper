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

## **new** and **init**

`__new__` : 在对象实例化前时候调用，返回一个对象
`__init__`: 在对象创建之后调用, 用来初始化对象属性

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
