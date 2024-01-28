---
author: facsert
pubDatetime: 2023-12-08 21:36:15
title: Python sqlalchemy
slug: Python sqlalchemy
featured: false
draft: false
tags:
  - Python
  - sqlalchemy
description: "Python ORM 模块 sqlalchemy"
---

<!--
 * @Author: facsert
 * @Date: 2023-12-08 21:36:15
 * @LastEditTime: 2023-12-13 20:10:34
 * @LastEditors: facsert
 * @Description:
-->

## Table of Contents

sqlalchemy 是 python 的ORM(Object-relational mapping 对象关系映射)框架,  
通过对象和数据库表之间进行映射，从而实现对象对数据库的操作.

## 安装

```bash
 $ pip install SQLAlchemy
 $ pip list | grep SQLAlchemy
 > SQLAlchemy           2.0.23
```

## 连接

`sqlalchemy` 会根据数据库驱动自动创建数据库文件并连接数据库  

```py
from sqlalchemy import create_engine

engine = create_engine(
    "mysql://user:password@localhost:3306/dbname",
    echo=True,                                   # True 打印出 sql 语句, 方便调试
    future=True,                                 # 使用 SQLAlchemy 2.0 API，向后兼容
    pool_size=5,                                 # 连接池大小默认为 5 个，设置 0 表示连接无限制
    pool_recycle=3600,                           # 设置时间, 限制数据库自动断开
)

engine = create_engine(
    "sqlite:////root/Desktop/sqilte.db",         # 创建 SQLite 的内存数据库, 路径使用绝对路径
    echo=True, future=True,                      # echo=True, 打印执行的 sql 语句
    connect_args={"check_same_thread": False}    # 须加上 check_same_thread=False，否则多线程中无法使用
)

```

## 创建表

`sqlalchemy` 内置 `declarative_base` 用于创建表的基类,  
所有继承该类的类都会在注册到内存后自动创建表  

创建 `models.py` 文件, 继承 `Base` 类, 定义表模型

```python
from sqlalchemy.ext.declarative import declarative_base


Base = declarative_base()                        # 声明表的基类, 所有表继承 Base

class User(Base):
    __tablename__ = 'users'                      # 表名

    id = Column(Integer, primary_key=True)       # 设置主键
    name = Column(String)
    age = Column(Integer)

    def __repr__(self):                          # 打印出表详细信息
        return f"<User(id={self.id}, name={self.name}, age={self.age})>"
```

注: 更多表数据类型 [数据类型](#表数据类型)  

创建 `sql.py` 文件, 进行数据库连接并创建表

```python
from sqlalchemy import create_engine

from models import Base                          # 引入表模型, 将表模型注册进内存

engine = create_engine(
    "sqlite:////root/Desktop/sqilte.db",
    echo=True, future=True,
    connect_args={"check_same_thread": False}
)
Base.metadata.create_all(engine, checkfirst=True)# 所有表注册进内存后, 创建表
```

在 `user.py` 使用数据库

```py
from sqlalchemy.orm import Session

from sql import engine
from models import User

db = Session(engine)                             # 创建数据库会话
db.query(User).all()                             # 数据库操作
db.close()                                       # 关闭数据库会话
```

## 插入数据

sqlalchemy 1.x 使用 db.add 添加数据  
sqlalchemy 2.x 使用 insert 生成 sql 语句, 然后使用 db.execute 执行

```python
from sqlalchemy import insert
from sqlalchemy.orm import declarative_base, sessionmaker, Session

db = Session(engine)                                            # 创建 Session 对象

db.add(User(name="Jason", age=20))                                   # sqlalchemy 1.x 插入单个数据
db.add_all([                                                         # sqlalchemy 1.x 插入多个数据
    User(name="Jason", age=20),
    User(name="Lily", age=18),
])
db.execute(insert(User), [                                           # sqlalchemy 2.x 插入数据
    {'name':"Jason", 'age':20},
    {'name':"Lily", 'age':18},
])

db.commit()                                                          # 增删改查之后都需要提交数据, 令修改生效
db.close()
```

```py
stmt = insert(User).values(name=Jason, age=20)                       # Insert.values 添加单个数据
stmt = insert(User).values([{'name':"Jason", 'age':20}])             # Insert.values 批量添加数据

stmt = insert(User).values(name=Jason, age=20).returning(User)       # Insert.returning, execute 时返回插入数据
db.execute(stmt)                                                     # 执行 sql 语句
```

## 查询数据

sqlalchemy 1.x 使用 db.query 查询数据

```python
from sqlalchemy import select

db.query(User).filter(User.age == 18).first()                        # sqlalchemy 1.x 获取单个数据
db.query(User).filter(User.age == 18).all()                          # sqlalchemy 1.x 批量获取数据

stmt = select(User).where(User.age == 18)                                
db.execute(stmt).scalar()                                            # sqlalchemy 2.x 获取单个数据
db.execute(stmt).scalars()                                           # sqlalchemy 2.x 批量获取数据

db.commit()

stmt = select(User).where(User.age == 18).order_by(User.id)          # 查询数据并按 id 排序
db.execute(stmt)
db.commit()
```

## 更新数据

```python
from sqlalchemy import update

user = db.query(User).filter(User.age == 18).first()                 # 通过 query 筛选出单个数据
user.age = 19                                                        # sqlalchemy 1.x 更新单个数据
db.query(User).filter(User.age == 18).update({"age": 19})            # sqlalchemy 1.x 批量更新

db.execute(update(User).where(User.age == 18), {"age": 19})          # sqlalchemy 2.x 批量更新
db.execute(update(User).where(User.age == 18).values(age=19))

db.commit()
db.close()
```

## 删除数据

```python
from sqlalchemy import delete

db.delete(db.query(User).filter(User.age == 18).first())             # sqlalchemy 1.x 删除单个数据
db.query(User).filter(User.age == 18).delete()                       # sqlalchemy 1.x 批量删除数据

db.execute(delete(User).where(User.age == 18))                       # sqlalchemy 2.x 批量删除数据

db.commit()
db.close()
```

## 拓展

### 表数据类型

```py
Integer     : int 整数类型
Float       : float 浮点类
Boolean     : bool 布尔类型, true, false
String      : string 字符和字符串的基类
Time        : datetime.time() 类型对象

SmallInteger: 较小的一种 int 整数
BigInteger  : 一种更大的 int 整数。
Double      : 一种更大的 float 浮点数

Date        : datetime.datetime() 类型对象
DateTime    : datetime.datetime() 类型对象
Interval    : datetime.timedelta() 类型对象

Text        : 大小可变的字符串类型。
Unicode     : 长度可变的Unicode字符串类型
UnicodeText : 无限长的Unicode字符串类型

Enum        : 枚举类型
ARRAY       : 数组类型 Column("data", ARRAY(Integer)) => {"data": [1,2,3]}
JSON        : 字典类型 Column('data', JSON) => {"data": {"a": 1, "b": 2}}
```

### 表模块封装

创建一个基类表, 并赋予一些基本功能  
便于表数据与基本数据的转换与变更  

```py
class Table(Base):
    __abstract__ = True

    id = Column(Integer, primary_key=True)

    def __init__(self):
        pass

    def get(self, key):
        """ 获取对象属性 """
        return getattr(self, key)

    def set(self, key, value):
        """ 设置对象属性 """
        return setattr(self, key, value)

    def update(self, items):
        """ 更新对象属性 """
        for key, value in items.items():
            setattr(self, key, value)
            
    def json(self):
        """ 对象转为 json """
        return {col.name: getattr(self, col.name, None) for col in self.__table__.columns}

    def __repr__(self):
        return f'<Table id:{self.id}>'

class User(Table):
    """ 继承表基类, 创建用户表 """

    __tablename__ = 'users'                      

    id = Column(Integer, primary_key=True)       
    name = Column(String)
    age = Column(Integer)

    def __init__(self, name, age):
        super().__init__()
        self.name = name

    def __repr__(self):                                             
        return f"<User(id={self.id}, name={self.name}, age={self.age})>"
```
