---
author: facsert
pubDatetime: 2024-02-21 20:34:43
title: Python pandas
slug: Python pandas
featured: false
draft: false
tags:
  - Python
  - pandas
description: "Python pandas 模块"
---

## Table of Contents

## 介绍

pandas 是一个用于处理表格型数据的 Python 库, 可以轻松地处理各种结构化数据

|   类型    |                        示例                         |   含义   |
| :-------: | :-------------------------------------------------: | :------: |
|  Series   |              s = pd.Series([1, 2, 3])               | 一维数组 |
| DataFrame | df = pd.DataFrame({'A': [1, 2, 3], 'B': [4, 5, 6]}) | 二维数组 |

```py
import pandas as pd

# 按列创建
pd.DataFrame({'A': [1, 2, 3], 'B': [4, 5, 6]})
   A   B
0  1   4
1  2   5
2  3   6

# 按行创建
pd.DataFrame([{'a': 1, 'b': 2}, {'a': 5, 'b': 10, 'c': 20}])
   a   b     c
0  1   2   NaN
1  5  10  20.0
```

## Series

Series 是一维数组, 可以理解为二维数组的一列或 DataFrame 取的一行

```py
# 创建 Series 数据, 不设置 index, 默认索引从 0 开始
pd.Series(["1st", "2nd", "3rd"])
> 0    1st
> 1    2nd
> 2    3rd

# 创建 Series 数据, 并指定列索引
pd.Series(["1st", "2nd", "3rd"], index=['a', 'b', 'c'])
> a    1st
> b    2nd
> c    3rd

s = pd.Series(["1st", "2nd", "3rd"], index=['a', 'b', 'c'])

# 按列索引取值
s['c'] / s.loc['c'] / s.iloc[2]
> '3rd'

# 按行索引取片段
s[:2] / s[['a', 'b']] / s.loc[['a', 'b']] /s.iloc[range(2)]
> a    1st
> b    2nd

# Series 重新赋值/追加值
s['d'] = '4th'

# Series -> list 转列表
s.tolist()
list(s.array) / list(s.values) / list(s)
> ['1st', '2nd', '3rd']

# Series 数据操作, 字符串叠加, 整数运算
s + s
s * 2
> a    1st1st
> b    2nd2nd
> c    3rd3rd
```

## DataFrame

DataFrame 表示一个大小可变的二维数据, 类似于一张表  
DataFrame 有行索引和列索引  
DataFrame 可容纳多种数据类型  
DataFrame 允许缺失值, 提供方法处理缺失值

```py
data = [
  {'name': 'A', 'age': '16', 'height': '1.75'},
  {'name': 'B', 'age': '17', 'height': '1.80'},
  {'name': 'C', 'age': '18', 'height': '1.85'},
  {'name': 'D', 'age': '19', 'height': '1.90'}
]

# index 使用自定义行索引(默认行索引是 0 开始的整数)
df = pd.DataFrame(data, index=['a', 'b', 'c', 'd'])
>    name age height
> a    A  16   1.75
> b    B  17   1.80
> c    C  18   1.85
> d    D  19   1.90

# DataFrame 的列索引, list(df.columns) 转列表
df.columns
Index(['name', 'age', 'height'], dtype='object')

# DataFrame 的行索引 list(df.index) 转列表
df.index
Index(['a', 'b', 'c', 'd'], dtype='object')

# DataFrame 取值 numpy 数组
df.values
array([['A', '16', '1.75'],
       ['B', '17', '1.80'],
       ['C', '18', '1.85'],
       ['D', '19', '1.90']], dtype=object)

# DataFrame 开头和结尾切片
df.head(3) / df.tail(3)

# df.fillna(value) 缺失值赋值
df.fillna(0)

# df.dropna() 删除包含缺失值的行
df.dropna()
```

### DataFrame 取值

```py
# DataFrame[column name] -> Serial 获取一列数据
df['name']
> a    A
> b    B
> c    C
> d    D

# 按行名字取行 DataFrame.loc[row name] -> Serial 获取一行数据
# 按行序号去行 DataFrame.iloc[row index] -> Serial 获取一行数据
df.loc['c']
df.iloc[2]
> name         C
> age         18
> height    1.85
> Name: c, dtype: object

# DataFrame.iloc[[row index list]] -> DataFrame 按行序号取多行
df.iloc[range(3)]
>    name age height
> a    A  16   1.75
> b    B  17   1.80
> c    C  18   1.85
```

```py
# 行遍历
for i, r in df.iterrows():
    print(f"{i}: {list(r)}")

> a: ['A', '16', '1.75']
> b: ['B', '17', '1.80']
> c: ['C', '18', '1.85']
> d: ['D', '19', '1.90']

for i, r in df.iterrows():
    print(f"{i}: {dict(r)}")

> a: {'name': 'A', 'age': '16', 'height': '1.75'}
> b: {'name': 'B', 'age': '17', 'height': '1.80'}
> c: {'name': 'C', 'age': '18', 'height': '1.85'}
> d: {'name': 'D', 'age': '19', 'height': '1.90'}

# 修改列
df['name'] = df['name'].apply(lambda x: "pre_" + x)
df['name'] = df['name'].transform(lambda x: "pre_" + x)
df['name'] = 'pre' + df['name']
>     name age height
> a  pre_A  16   1.75
> b  pre_B  17   1.80
> c  pre_C  18   1.85
> d  pre_D  19   1.90

# DataFrame 合并
df = pd.concat([df1, df2])

# DataFrame 追加 Series 行
df.append(s, ignore_index=True)

# 插入列, 插入序号, 列名, 可迭代数据
df.insert(index, 'name', iterable)
df.insert(3, "count", [3, 2, 5, 1])
>    name age height  count
> a    A  16   1.75      3
> b    B  17   1.80      2
> c    C  18   1.85      5
> d    D  19   1.90      1

# 结尾追加列, 该列的值全为 3
df['column'] = 3


# 删除列, inplace=True 会直接修改原数据
df.drop(columns=['name'], inplace=True)
>     name age height
> a     A  16   1.75
> b     B  17   1.80
> c     C  18   1.85
> d     D  19   1.90
```

### DataFrame 分组聚合

```py
# 排序
df.sort_values(by="age")
  name age height
a    A  16   1.75
b    B  17   1.80
c    C  18   1.85
d    D  19   1.90

df = pd.DataFrame({
    'category': ['A', 'A', 'B', 'B', 'C', 'C'],
    'value': [10, 20, 30, 40, 50, 60]
})

  category  value
0        A     10
1        A     20
2        B     30
3        B     40
4        C     50
5        C     60

result = df.groupby('category')['value'].agg(['max', 'min', 'mean'])
          max  min  mean
category
A          20   10  15.0
B          40   30  35.0
C          60   50  55.0

## 分组聚合
data = {
    'name': ['张三', '张三', '李四', '李四', '王五', '王五'],
    'address': ['北京市', '北京市', '上海市', '上海市', '广州市', '广州市'],
    'age': [25, 25, 30, 32, 35, 35],
    'height': [170, 175, 180, 185, 165, 170],
    'date': ['2024-03-21', '2024-03-22', '2024-03-21', '2024-03-22', '2024-03-21', '2024-03-22']
}
df = pd.DataFrame(data)
# 筛选分组的列, 指定列数据操作(max: 最大值, min: 最小值, mean: 平均值, first: 第一个值)
df = df.groupby(["name", "address", "age"]).agg({"height": ["max", "min", "mean"], "date": "first"})
                           height          date
                        max  min   mean    first
name address     age
张三   北京市     25     175  170  172.5  2024-03-21
李四   上海市     30     180  180  180.0  2024-03-21
                 32     185  185  185.0  2024-03-22
王五   广州市     35     170  165  167.5  2024-03-21

# 分组后指定列索引会提升
df.columns
MultiIndex([('height',   'max'),
            ('height',   'min'),
            ('height',  'mean'),
            (  'date', 'first')],
           )

# 重新定义列索引后恢复
df.columns = ["height_max", "height_min", "height_mean", "date"]
df.reset_index(inplace=True)
    name    address  age     height_max  height_min  height_mean  date
0   张三     北京市   25         175         170        172.5  2024-03-21
1   李四     上海市   30         180         180        180.0  2024-03-21
2   李四     上海市   32         185         185        185.0  2024-03-22
3   王五     广州市   35         170         165        167.5  2024-03-21

# agg 可用参数
# first/last 最初非 None 值/最后一个非 None 值
# count/size 组中非 None 值的数量/组中数据的数量(包含 None 值)
# sum 该分组所有值得总和
# max/min/mean  最大值/最小值/平均值
# std/var 标准差/方差
# median/quantile  中位数/分位数
```
