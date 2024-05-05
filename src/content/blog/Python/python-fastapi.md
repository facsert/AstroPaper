---
author: facsert
pubDatetime: 2023-10-28 14:52:02
title: Python fastapi
slug: Python fastapi
featured: false
draft: false
tags:
  - Python
  - fastapi
description: "Python Web 框架 fastapi"
---

<!--
 * @Author       : facsert
 * @Date         : 2023-10-28 14:52:02
 * @LastEditTime: 2023-11-22 20:36:48
 * @LastEditors: facsert
 * @Description:
-->

## Table of Contents

## 介绍

FastAPI 是一个现代、快速、开源的 Web 框架，用于构建高性能的 RESTful API。

## 安装

```bash
 $ python -m pip install "fastapi[all]"

 tree .
├── config
├── lib                                          # 存放自定义模块(无变动或变动少)
│   └── common.py
├── LICENSE
├── log                                          # 存放日志文件
├── temp                                         # 存放临时文件
├── static                                       # 静态文件
├── .gitignore                                   # git 忽略文件
├── main.py                                      # 启动文件
├── README.md
├── requirement.txt                              # 项目依赖包
├── api                                          # api 路由
│   └── v1
│       └── node.py
└── utils                                        # 自定义模块
    ├── database.py                              # 数据库连接
    ├── logger.py                                # logger 初始化
    ├── middleware.py                            # 中间件
    ├── router.py                                # 路由配置
    └── schemas.py                               # fastapi 参数模型
```

## fastapi 入口

```py
from contextlib import asynccontextmanager

import uvicorn
from loguru import logger
from fastapi import FastAPI

from utils.logger import logger_setting
from utils.router import add_routers
from utils.database import Database
from utils.middleware import add_middlewares

HOST = "localhost"
PORT = 8010

@asynccontextmanager
async def lifespan(router: FastAPI):
    """ 应用开启和结束操作 """
    logger_setting()
    Database.init()
    add_routers(router)
    logger.info("app start")
    yield
    logger.info("app close")
    Database.close()

app = FastAPI(
    title="FastAPI",
    description=f"{HOST}:{PORT} api",
    version="0.0.1",
    lifespan=lifespan
)

add_middlewares(app)


if __name__ == "__main__":
    uvicorn.run("main:app", host=HOST, port=PORT, reload=True)
```

浏览器打开 `http://0.0.0.0:8010/`  
swagger-ui `http://0.0.0.0:8010/docs/`

## swagger-ui

[Github swagger-ui](https://github.com/swagger-api/swagger-ui.git)

打开 swagger 页面需要加载外界 CDN 资源，可能会非常慢，建议使用离线 swagger-ui 文件  
在 Github 下载官方 swagger-ui 资源, 放入项目 `static` 路径下, 在代码中配置即可

```bash
project
├─ static
│  └── swagger-ui
│      ├── ...
│      └── README.md
└── main.py
```

在 `utils/router.py` 中挂载 swagger 并配置可用路由

```py
from fastapi import FastAPI
from fastapi.staticfiles import StaticFiles
from fastapi.openapi.docs import get_swagger_ui_html

from api.v1 import student, article
from lib.common import abs_dir

def add_routers(app: FastAPI):
    """ app add router"""
    # 添加路由
    app.include_router(student.router, prefix="/api/v1", tags=["student"])
    app.include_router(article.router, prefix="/api/v1", tags=["article"])

    # mount swagger 静态文件
    app.mount('/static', StaticFiles(
        directory=abs_dir("static", "swagger-ui", "dist")),
        name="static"
    )

    # 定义 swagger 挂载路由
    @app.get("/", include_in_schema=False)
    async def custom_swagger_ui_html():
        """ set local static swagger """
        return get_swagger_ui_html(openapi_url=app.openapi_url, title=app.title)
```

## database

定义数据连接和使用

```py
import psycopg
from psycopg import Cursor, Connection
from psycopg.rows import dict_row

class Database:

    connect: None | Connection = None

    @classmethod
    def connect_database(cls) -> None:
        cls.connect = psycopg.connect(
            host="localhost",
            port="5432",
            dbname="learn",
            user="postgres",
            password="password"
        )

    @classmethod
    def create_session(cls):
        session = cls.connect.cursor(row_factory=dict_row)
        yield session

        session.close()
        cls.connect.commit()

    @classmethod
    def get_session(cls) -> Cursor:
        return cls.connect.cursor(row_factory=dict_row)

    @classmethod
    def create_tables(cls):
        pass

    @classmethod
    def close(cls):
        cls.connect.close()

    @classmethod
    def init(cls):
        cls.connect_database()
        cls.create_tables()
```

## api 路由

定义 fastapi api 路由

```py
from fastapi import APIRouter, Depends
from loguru import logger
from psycopg import Cursor

from utils.database import Database
from utils.schemas import Student

router = APIRouter()
created = Database.create_session

@router.get("/articles")
def get_articles(session: Cursor = Depends(created)):
    return session.execute("SELECT * FROM article").fetchall()

@router.get("/articles/{id}")
def get_article(id: int, session: Cursor = Depends(created)):
    return session.execute("SELECT * FROM article WHERE id = %s", (id,)).fetchone()
```

## 日志

设置日志打印格式, 输出到指定文件

```py
from loguru import logger

from lib.common import abs_dir

def logger_setting():
    """ 设置 logger """
    logger.remove()

    fmt = '[<level>{level: <8}</level>][<green>{time:YYYY-MM-DD HH:mm:ss}</green>]: <level>{message}</level>'
    logger.add(sys.stderr,  level='INFO', format=fmt)
    logger.add(abs_dir('log', 'report.log'),
        level='INFO', format=fmt, rotation='1 week', retention='30 days'
    )
```

## 中间件

自定义请求开始和请求结束后的操作

```py
from traceback import format_exc

from fastapi.middleware.cors import CORSMiddleware
from fastapi import FastAPI, Request
from fastapi.responses import JSONResponse
from loguru import logger


def add_middlewares(app: FastAPI):
    """ 允许所有请求 """
    app.add_middleware(
        CORSMiddleware,
        allow_origins=["*"],
        allow_credentials=True,
        allow_methods=["*"],
        allow_headers=["*"],
    )

    @app.middleware("http")
    async def catch_exceptions(request: Request, call_next):
        """ 捕获所有接口执行异常 """
        try:
            response = await call_next(request)
            return response
        except Exception as e:
            logger.error(format_exc())
            return JSONResponse(status_code=500, content={"message": "Server run error"})
```
