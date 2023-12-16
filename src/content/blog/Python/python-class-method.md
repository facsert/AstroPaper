---
author: facsert
pubDatetime: 2022-07-24 12:08:37
title: Python Class
postSlug: ""
featured: false
draft: false
tags:
  - Python
description: "Python 类方法"
---

<!--
 * @Author       : facsert
 * @Date         : 2022-07-24 12:08:37
 * @LastEditTime : 2023-07-28 17:05:27
 * @Description  : edit description
-->

## 引申

开发的时候用到 logging 模块, 重新封装后发现使用有点麻烦

1. 多个模块都会用到该模块, logging 多次实例化会导致重复打印
2. 使用前需要先获取对象, 无法直接使用其方法
3. 只能实例化一次, 导致模块间使用需要频繁传递该对象

综上我在思考能否 import 后直接使用, 且不会多次实例化  
通过查询资料了解到**类方法**和**静态方法**

注: logging 模块可以统一输出格式, 打印在窗口并写入指定文件

## 区别

| 方法类型 |  参数  |     装饰器      |
| :------: | :----: | :-------------: |
| 实例方法 | `self` |       无        |
|  类方法  | `cls`  | `@classmethod`  |
| 静态方法 |   无   | `@staticmehtod` |

## 代码示例

```python
class People:

    def obj_func(self):                          # 实例方法
        print(f'obj function: {self} \n')

    @classmethod                                 # 类方法
    def class_func(cls):
        print(f'class function: {cls} \n')

    @staticmethod                                # 静态方法
    def static_func():
        print('static function')

if __name__ == '__main':
    facser = People()
    facser.obj_func()
    facser.class_func()
    facser.static_func()
```

```python
 > obj function: <__main__.People object at 0x000002673D477CA0>
 >
 > class function: <class '__main__.People'>
 >
 > static function
```

## 实例方法

最常见的方法, 方法的第一个参数为 `self`, 表示实例对象  
通过 `self` 即可调用实例对象属性和方法

```python
class People:

    name = 'kertory'                             # 定义类属性 name

    def obj_func(self):
        print(f'obj func: {self.name}')          # 打印对象属性 name

    @classmethod
    def class_func(cls):
        print(f'class func: {cls.name}')         # 打印类属性 name

    @staticmethod
    def static_func():                           # 静态方法
        print('static function')

if __name__ == '__main':
    facser = People()                            # 类实例化成对象 facser
    facser.obj_func()                            # facser 对象调用实例化方法
    facser.class_func()                          # facser 对象调用类方法
```

```python
 > obj func: kertory                             # 实例方法打印
 > class func: kertory                           # 类方法打印
 > static function                               # 静态方法打印
```

实例化对象继承了类的属性  
实例化对象是可以调用**类方法**和**静态方法**的

## 类方法

类方法在开头添加 `@classmethod` 装饰器  
第一个参数 `cls` 表示类本身

```python

class People:

    name = 'kertory'                             # 定义类属性 name

    def obj_func(self):
        print(f'obj func: {self.name}')          # 打印对象属性 name

    @classmethod
    def class_func(cls):
        print(f'class func: {cls.name}')         # 打印类属性 name

    @staticmethod
    def static_func():                           # 静态方法
        print('static function')

if __name__ == '__main':
    People.class_func()                          # 类调用类方法
    People.static_func()                         # 类调用静态方法
    People.obj_func()                            # 类调用实例方法
```

```python
 > class func: kertory                           # 成功执行类方法
 >
 > static function                               # 成功执行静态方法
 > Traceback (most recent call last):
 >  File "E:\文档\Python\python_class.py", line 36, in <module>
 >    People.obj_func()
 > TypeError: obj_func() missing 1 required positional argument: 'self'
```

类可以使用类方法, 类属性和静态方法  
类无法使用实例属性和实例方法, 后两者须实例化后才能使用

## 静态方法

静态方法需要在定义时添加 `@staticmethod` 装饰器  
静态方法无法调用类或者实例化对象的属性和方法, 仅是一个普通函数

## 总结和实践

### 总结

- 静态方法属于类, 却无法使用类的属性和方法, 可以被类与对象使用
- 类方法可调用类属性, 类方法和静态方法, 可以被类与对象使用
- 实例化方法可调用类内所有属性方法, 但只可以被实例化对象使用

| 类型 | 可用范围                                     |
| :--: | :------------------------------------------- |
|  类  | 类方法, 类属性; 静态方法                     |
| 对象 | 实例方法, 实例属性; 类方法, 类属性; 静态方法 |

### logging 实践

```python

class logger:

    obj_log = None

    def info(cls, msg):                          # 定义类方法, 类可以直接使用
        try:
            cls.obj_log.info(msg)                # 正常执行, 表示 logger 类已创建
        except Exception as _:
            cls.create_logger()                  # 执行失败则创建 logger 类后执行
            cls.obj_log.info(msg)
        return msg

    def terminal_handle(cls):                    # 设置窗口打印格式
        pass

    def file_handle(cls):                        # 设置文本写入打印格式
        pass

    def create_logger(cls):                      # 自定义 logger 对象, 赋值给类属性 obj_log
        cls.obj_log = logging.getLogger()
        cls.terminal_handle()
        cls.file_handle()
```

通过类方法解决开头的难点

1. 仅 `create` 一次即可, 实例化对象保存在类属性 `obj_log` 中
2. 无需实例化封装对象, `import` 后使用类方法即可
3. 无需传递对象, 仅 `import` 即可使用
