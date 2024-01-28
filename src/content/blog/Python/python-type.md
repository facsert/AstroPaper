---
author: facsert
pubDatetime: 2023-09-25 15:28:43
title: Python Type
slug: Python Type
featured: false
draft: false
tags:
  - Python
description: "Python 类型注解"
---

<!--
 * @Author       : facsert
 * @Date         : 2023-09-25 15:28:43
 * @LastEditTime : 2023-07-28 11:50:12
 * @Description  : edit description
-->

## Table of Contents

## 引申

### 动态语言与静态语言

- 动态语言(弱类型语言)
- 静态语言(强类型语言)

动态语言的变量没有类型, 变量的类型由值的类型决定

```python
 name = "facser"                                 # python 是动态语言
 name = 18                                       # 变量无需声明类型, 可接受任意类型的值
 name = [1,2,3]                                  # 变量可随意变换类型, 类型仅在执行时确定
```

静态语言变量有类型, 且变量类型和值的类型必须一致

```go
 var name string = "facser"                      // Go 是静态语言
 var age int = 18                                // 变量声明需要确定变量类型
 age = 20                                        // 变量重新赋值时, 变量和值的类型必须一致
```

### python 类型注解

python 类型注解:

- 便于快速理解代码
- 编辑器的静态类型检查
- 编辑器类型提示快速开发

注: python 不会做类型检查, 所有类型注解被看做注释用于提示, python 仍然可以使用动态语言特性

## 变量注解

```Python
 var_str: str = "string"                        # 字符串变量
 var_byte:bytes = b'byte'                       # byte 类型变量
 var_int: int = 3                               # 整形变量
 var_float: float = 1.6                         # 浮点型变量
 var_bool: bool = True                          # 布尔类型变量
 none_var: None = None                          # 定义 None 值

 var_any: Any = 'any type'                      # 任意类型(无法确定类型时使用, 减少使用)
```

### 容器类型

```Python
  var_list: list[str] = ['a', 'b', 'c']          # 列表的值类型只能是字符串
  var_set: set[int] = {1,2,3}                    # 集合的值类型只能是整型
  var_dict: dict[str, int] = {'key': 3}          # 字典的 key 为字符串, 值为字符串

  var_tuple: tuple[int, str] = [3, 'c']          # 元组类型声明和值数量和类型均需要一致
  var_tuple2: tuple[int, str] = ['a', 3, 1]      # 错误, 序号1 2值与类型不符, 类型与值数量不一致
```

注: 上述除元组与字典外, 容器元素只能指定一个类型. 元组需要数量和类型一致, 字典只能使用一组 key value 类型
(python3.9版本前容器类型需要导入 typing 模块的 List Tuple Set Dict)

### 联合类型

- Union
- Optional

```python
 from typing import Union
 str_int: Union[str, int] = 2                    # 变量可为整形也可为字符串

 lst: Union[str, int] = [2, 'a']                 # 列表的值类型可为字符串或整形

 str_int: str | int = 2                          # python3.10 的新写法更直观
```

```python
 option_str: Optional[str] = 'a'                     # 可选类型, 值可为字符串或者 None
 option_int: Optional[int] = None                    # 可选类型, 值可为整形或者 None

 Optional[int] = Union[int, None]                    # Optional 自带 None, 只能再添加一种类型

 optional_int: int | None = 3                        # python3.10 的新写法
```

### 抽象类型

- Mapping
- MutableMapping
- Sequence
- Iterable

数组, 元组, 集合

### 函数注解

```Python
 def func(name: str, age: int) -> dict:          # 参数添加类型, 返回值添加类型
     return {'name': name, 'age': age}

  def func(name: str, age: int=18) -> str, int:  # 返回多个值
      return name, age
```

### Callable

函数, 匿名函数, 包含 **call** 方法的对象

Callable[[args_type], return_type]

```Python
 from typing import Callable

 def func(name: str) -> None:                    # 函数类型注解, 无返回值则默认返回 None
     print(f"hellow {name}")

  foo: Callable[[str], None] = func              # 函数类型注解, 第一个参数是函数参数类型, 第二个是返回值类型

  fun: Callable[[int], bool] = lambda x: x is 1  # 匿名函数类型注解
```

### 泛型

当对函数使用类型注解时, 参数或返回值有多种类型的可能, 我们会使用 Union 联合类型
但是, 这依然会出现问题, 如我们希望函数参数和返回值类型应当一致

```python
 def foo(a:str | int, b:str | int) -> str | int: # 参数可以是整形也可以是字符串
  return a + b                                   # 可能出现 a b 类型不一致情况

 from typing import TypeVar

 T = TypeVar('T', int, str)                      # T 类型可以字符串或整数, 第一个参数和变量名一致(T 'T')
 def foo(a: T, b: T) -> T:                       # a b 及返回值类型一致
     return a + b

 any = TypeVar('any')                            # 任意类型的泛型
```

泛型可以使变量表示多种类型同时约束使用该类型的变量为同一类型

### 自定义类型

```python
 from typing import NewType

matrix = NewType('matrix', list[list[int]])      # 自定义类型并命名
nums = NewType('nums', list[int])
strings = NewType('strings', str)

a: matrix = matrix([[1,2], [2,3]])               # 需要实例化赋值
b: nums = nums([1,2,3])
c: strings = strings('str')
```

### 类型别名

```python

 matrix = list[list[int]]                        # 自定义类型并命名
 nums = list[int]
 strings = str

 a: matrix = matrix([[1,2], [2,3]])              # 无需实例化与正常类型解一致
 b: nums = nums([1,2,3])
 c: strings = strings('str')

```

### class

通过类来定义复杂的组合类型, 类似接口

```python
 from pydantic import BaseModel

 class Person(BaseModel):
     id: int
     name: str
     age: int
     email: str

 victory: Person = Person(
     id = 3,
     name = 'victory',
     age = 18,
     email = 'victory@example.com',
 )
```
