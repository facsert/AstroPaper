---
author: facsert
pubDatetime: 2023-12-08 21:36:15
title: Python sqlalchemy
postSlug: ""
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

sqlalchemy 是 python 的ORM(Object-relational mapping 对象关系映射)框架,  
通过对象和数据库表之间进行映射，从而实现对象对数据库的操作.

## 安装

```bash
 $ pip install SQLAlchemy
 $ pip list | grep SQLAlchemy
 > SQLAlchemy           2.0.23
```

## 连接

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

```python
from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy import Column, Integer, String

engine = create_engine(
    "sqlite:////root/Desktop/sqilte.db",
    echo=True, future=True,
    connect_args={"check_same_thread": False}
)

Base = declarative_base()                        # 声明表的基类, 所有表继承 Base
Base.metadata.create_all(engine, checkfirst=True)# 创建表

class User(Base):
    __tablename__ = 'users'                      # 表名

    id = Column(Integer, primary_key=True)       # 设置主键
    name = Column(String)
    age = Column(Integer)

    def __repr__(self):                          # 打印出表详细信息
        return f"<User(id={self.id}, name={self.name}, age={self.age})>"
```

基本数据类型

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

## 插入数据

sqlalchemy 1.x 使用 session.add 添加数据  
sqlalchemy 2.x 使用 insert 生成 sql 语句, 然后使用 session.execute 执行

```python
from sqlalchemy import insert
from sqlalchemy.orm import declarative_base, sessionmaker, Session

session = Session(engine)                                            # 创建 Session 对象

session.add(User(name="Jason", age=20))                              # sqlalchemy 1.x 插入单个数据
session.add_all([                                                    # sqlalchemy 1.x 插入多个数据
    User(name="Jason", age=20),
    User(name="Lily", age=18),
])
session.execute(insert(User), [                                      # sqlalchemy 2.x 插入数据
    {'name':"Jason", 'age':20},
    {'name':"Lily", 'age':18},
])

session.commit()                                                     # 增删改查之后都需要提交数据, 令修改生效
```

```py
stmt = insert(User).values(name=Jason, age=20)                       # Insert.values 添加单个数据
stmt = insert(User).values([{'name':"Jason", 'age':20}])             # Insert.values 批量添加数据

stmt = insert(User).values(name=Jason, age=20).returning(User)       # Insert.returning, execute 时返回插入数据
session.execute(stmt)                                                # 执行 sql 语句
session.commit()                                                     # 提交数据, 令修改生效
```

## 查询数据

sqlalchemy 1.x 使用 session.query 查询数据

```python
from sqlalchemy import select

session.query(User).filter(User.age == 18).first()                   # sqlalchemy 1.x 获取单个数据
session.query(User).filter(User.age == 18).all()                     # sqlalchemy 1.x 批量获取数据

stmt = select(User).where(User.age == 18)                            #
session.execute(stmt).scalar()                                       # sqlalchemy 2.x 获取单个数据
session.execute(stmt).scalars()                                      # sqlalchemy 2.x 批量获取数据

session.commit()
```

```py
stmt = select(User).where(User.age == 18).order_by(User.id)          # 查询数据并按 id 排序
session.execute(stmt)
session.commit()
```

## 更新数据

```python
from sqlalchemy import update

user = session.query(User).filter(User.age == 18).first()            # 通过 query 筛选出单个数据
user.age = 19                                                        # sqlalchemy 1.x 更新单个数据
session.query(User).filter(User.age == 18).update({"age": 19})       # sqlalchemy 1.x 批量更新

session.execute(update(User).where(User.age == 18), {"age": 19})     # sqlalchemy 2.x 批量更新
session.execute(update(User).where(User.age == 18).values(age=19))

session.commit()
```

## 删除数据

```python
from sqlalchemy import delete

session.delete(session.query(User).filter(User.age == 18).first())   # sqlalchemy 1.x 删除单个数据
session.query(User).filter(User.age == 18).delete()                  # sqlalchemy 1.x 批量删除数据

session.execute(delete(User).where(User.age == 18))                  # sqlalchemy 2.x 批量删除数据

session.commit()
```

## 自定义封装

```python
from sqlalchemy import create_engine, select, update, delete, insert, Column, Integer, String
from sqlalchemy.orm import declarative_base, sessionmaker, Session

path = "/root/Desktop/Python/fastapi/database/fastapi.db"
engine = create_engine(f"sqlite:///{path}",
    echo=True,future=True,
)

Base = declarative_base()
Base.metadata.create_all(engine, checkfirst=True)

class DB:
    def __new__(cls) -> None:
        cls.session = Session(engine)

    @classmethod
    def insert(cls, table: Base, *lines:dict):
        array = cls.session.execute(insert(table).returning(table), lines)
        cls.session.commit()
        return array.all()

    @classmethod
    def select(cls, table: Base, expr: str, first:bool=False):
        sql = select(table).where(eval(expr) if expr else True)
        return cls.session.scalar(sql) if first else cls.session.scalars(sql)

    @classmethod
    def update(cls, table: Base, expr: str, *lines:dict):
        sql, lines = (update(table).where(eval(expr)), lines) if expr else (update(table), list(lines))
        cls.session.execute(sql, lines)
        cls.session.commit()

    @classmethod
    def delete(cls, table: Base, expr: str):
        array = cls.session.execute(delete(table).where(eval(expr)).returning(table))
        cls.session.commit()
        return array.all()

class DB:

    def __new__(cls) -> None:
        cls.session = sessionmaker(bind=engine)()

    @classmethod
    def add(cls, *lines: Base):
        cls.session.add_all(list(lines))
        cls.session.commit()

    @classmethod
    def select(cls, table: Base, expression: str, first: bool=False):
        table = cls.session.query(table)
        array = table.filter(eval(expression)) if expression else table
        return array.first() if first else array

    @classmethod
    def update(cls, table: Base, expression:str, target:dict, first: bool=False):
        if first:
            line, key, value = cls.select(table, expression, True), target.popitem()
            setattr(line, key, value)
        else:
            cls.session.query(table).filter(eval(expression)).update(target)
        cls.session.commit()

    @classmethod
    def delete(cls, table: Base, expression:str, first: bool=False):
        if first:
            cls.session.delete(cls.select(table, expression, True))
        else:
            cls.session.query(table).filter(eval(expression)).delete()
        cls.session.commit()

class User(Base):
    __tablename__ = 'users'

    id = Column(Integer, primary_key=True)
    name = Column(String)
    age = Column(Integer)

    def __repr__(self):
        return f"<User(id={self.id}, name={self.name}, age={self.age})>"


if __name__ == "__main__":
    pass
```
