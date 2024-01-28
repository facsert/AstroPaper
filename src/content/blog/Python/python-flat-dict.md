---
author: facsert
pubDatetime: 2022-07-25 20:08:15
title: Python flat dictionary
slug: Python flat dictionary
featured: false
draft: false
tags:
  - Python
description: "Python 扁平化字典"
---

<!--
 * @Author       : facsert
 * @Date         : 2022-07-25 20:08:15
 * @LastEditTime: 2023-09-27 21:05:42
 * @Description  : edit description
-->
## Table of Contents

## 引申

字典经常被用来存取数据, 键值对的组合非常便于使用  
一个字典可以存储大量数据, 为了便于区分还可以层层分级, 多层嵌套

对于多层字典存取比较麻烦  
插入值多层的数据的时候需要考虑上层是否存在

能否简化深层字典的存取方式  
插入值的时候能否忽略层级问题, 自动生成多级数据

## 字典扁平化

如何简单快捷的进行快速存取深层字典呢?  
能否将字典简化成单层结构, 字典内就是 key 和 value

```python
def flat_dict(dic):
    for key, value in dic.items():
        if isinstance(value, dict):
            for k, v in flat_dict(value):
                k = '{key}.{k}'.format(key=key, k=k)
                yield (k, v)
        else:
            yield (key, value)
```

```python
if __name__ == '__main__':
    depth_dict = {'a':{'b': {'c': 0}, 'd':1}, 'c': 1}
    print(dict(flat_dict(depth_dict)))

>>> {'a.b.c': 0, 'a.d': 1, 'c': 1}
```

通过递归循环遍历深层字典, 把多层 key 通过分隔符连接  
但是, 这样扁平化无法获取字典类型的 value

```python
def flat_dict(dic):
    for key, value in dic.items():
        yield (key, value)
        if isinstance(value, dict):
            for k, v in flat_dict(value):
                k = '{key}.{k}'.format(key=key, k=k)
                yield (k, v)
```

```python
if __name__ == '__main__':
    depth_dict = {'a':{'b': {'c': 0}, 'd':1}, 'c': 1}
    print(dict(flat_dict(depth_dict)))

>>> {'a': {'b': {'c': 0}, 'd': 1}, 'a.b': {'c': 0}, 'a.b.c': 0, 'a.d': 1, 'c': 1}
```

增加了字典容量, 但是保存了所有 key 的值

## 字典存取

能将字典扁平化后, 考虑如何存取  
魔改魔术方法 `setitem` 和 `getitem` 通过 [] 存取数据

保留字典原有属性和方法, 新数据类型继承字典类

```python

class Flat(dict):

    def __init__(self, depth):
        dict.__init__(self, depth)
        self.flat = OrderedDict()
        self.char_split = '.'

    def flat_dict(self):
        pass

    def update_dict(self, key, value):
        pass

    def __setitem__(self, key, value):
        pass

    def __getitem__(self, key):
        pass
```

### 写入

- 解析扁平化 key 生成深度字典, 且更新到深度字典
- 把数据写入深度字典和扁平化字典

```python
    def __setitem__(self, key, value):
        self.update_dict(key, value)

    def update_dict(self, key, value):
        key_list = key.split(self.char_split)
        first, last = key_list[0], key_list[-1]

        dic = self
        for k in key_list[:-1]:
            dic.setdefault(k, {})
            if not isinstance(dic[k], dict):
                dic.update({k: {}})
            dic = dic[k]

        dic.update({last: value})
        self.flat.update(self.flat_dict({first: self[first]}))
```

### 取出

```python
    def __getitem__(self, key):
        try:
            return dict.__getitem__(self, key)
        except KeyError:
            return self.flat[key]
```

## 实现

`__str__` 能直接格式化打印结果
添加自定义分隔符

```python
class FlatDict(dict):
    """扁平化字典"""

    def __init__(self, *args, **kwargs):
        '''
        Description: 初始化属性, flat(扁平化字典) separator(分隔符)
        Return: None
        Attention: 对象存一个原生字典和扁平化字典
        '''
        super().__init__(*args, **kwargs)
        super().update(*args, **kwargs)
        self.flat = {}
        self.separator = "."
        self.flat_dict(self)

    def update_dict(self, key, value):
        '''
        Description: 解析 key, 将多层 key 逐层解析写入原生字典
        Param key str: 字典 key, 多层 key 包含分隔符
        Param value Any: 字典 value
        Return: None
        Attention:
        '''
        dic = self
        keys = key.split(self.separator)
        for k in keys[:-1]:
            dic.setdefault(k, {})
            if not isinstance(dic[k], dict):
                dic.update({k: {}})
            dic = dic[k]

        dic[keys[-1]] = value
        self.flat_dict(self)

    def flat_dict(self, dic, parent_key=''):
        '''
        Description: 原生字典多层 key 通过分隔符连接写入 flat 字典
        Param dic dict: 原生字典
        Param parent_key dict: 父字典的 key
        Return: None
        Attention: 任一层的字典 key value 都要保存
        '''
        for key, value in dic.items():
            new_key = f"{parent_key}{self.separator}{key}" if parent_key else key
            self.flat[new_key] = value
            if isinstance(value, dict):
                self.flat_dict(value, new_key)

    def __setitem__(self, key, value):
        '''
        Description: 字典 [] 方式设置值
        Param key str: 原生字典
        Param value Any: 父字典的 key
        Return: None
        Attention:
        '''
        if self.separator in key:
            self.update_dict(key, value)
        else:
            super().__setitem__(key, value)

    def __getitem__(self, key):
        '''
        Description: 字典 [] 获取值
        Param key str: 字典 key, 允许使用多层 key
        Return Any: 字典 key 对应的 value
        Attention:
        '''
        try:
            return super().__getitem__(key)
        except KeyError:
            return self.flat[key]

    def __delitem__(self, key):
        '''
        Description: 字典删除 key-value
        Param key str: 字典 key, 不允许使用多层 key
        Return: None
        Attention: 只允许使用原生字典的 key
        '''
        super().__delitem__(key)
        self.flat = {}
        self.flat_dict(self)

    def __len__(self):
        '''
        Description: 获取原生字典长度
        Return int: 字典长度
        Attention:
        '''
        return super().__len__()

    def update(self, *args, **kwargs):
        '''
        Description: 更新字典
        Return: None
        Attention: 用法与原生字典一致
        '''
        super().update(*args, **kwargs)
        self.flat = {}
        self.flat_dict(self)

    def get(self, key, default=None):
        '''
        Description: 扁平字典 get 方法
        '''
        return self.flat.get(key, default)

    def __str__(self):
        '''
        Description: json 格式原生字典
        Return srt: 字典字符串
        Attention:
        '''
        return dumps(self, indent=4)
```

```python
flat = Flat()
flat['a.b.c'] = 1
flat['b.c.a'] = 2
print(flat['a.b'])
print(flat['b.c.a'])
print(flat)


{'c': 1}
2
{
    "a": {
        "b": {
            "c": 1
        }
    },
    "b": {
        "c": {
            "a": 2
        }
    }
}
```
