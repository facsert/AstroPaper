---
author: facsert
pubDatetime: 2023-11-23 08:58:24
title: Python loguru
postSlug: ""
featured: false
draft: false
tags:
  - Python
description: "Python 日志模块 loguru"
---

<!--
 * @Author       : facsert
 * @Date         : 2023-11-23 08:58:24
 * @LastEditTime : 2023-12-13 22:33:52
 * @Description  : edit description
-->

# loguru

## 介绍

loguru 是一个第三方记录日志的 Python 库, 可以简单快速配置日志记录.

```bash
 $ python -m pip install loguru
 $ pip list | grep loguru
 > loguru             0.7.2
```

```py
from loguru import logger

logger.info("this is a info log")
logger.error("this is a error log")
logger.debug("this is a debug log")

2023-11-23 09:04:56.748 | INFO     | __main__:<module>:10 - this is a info log
2023-11-23 09:04:56.749 | ERROR    | __main__:<module>:11 - this is a error log
2023-11-23 09:04:56.749 | DEBUG    | __main__:<module>:12 - this is a debug log
```

## 配置输出

默认输出包含多个内容和设定

```bash
level : `'DEBUG'`                                                              # 只显示 DEBUG 界别上的 log
format:  '<green>{time:YYYY-MM-DD HH:mm:ss.SSS}</green> | <level>{level: <8}</level> | <cyan>{name}</cyan>:<cyan>{function}</cyan>:<cyan>{line}</cyan> - <level>{message}</level>'
  time : '<green>{time:YYYY-MM-DD HH:mm:ss.SSS}</green>'                       # 2023-11-23 09:04:56.749
  level: '<level>{level: <8}</level>'                                          # INFO     左对齐, 8 个字符, 空格补全
  model: '<cyan>{name}</cyan>:<cyan>{function}</cyan>:<cyan>{line}</cyan>'     # __main__:<module>:10 函数名, 行号
  msg  : '<level>{message}</level>'                                            # this is a info log 打印 log
```

|   Tag    |  TRACE  |  DEBUG  |  INFO  |  SUCCESS  |  WARNING  |  ERROR  |  CRITICAL  |
| :------: | :-----: | :-----: | :----: | :-------: | :-------: | :-----: | :--------: |
| Severity |    5    |   10    |   20   |    25     |    30     |   40    |     50     |
|  methon  | `trace` | `debug` | `info` | `success` | `warning` | `error` | `critical` |

默认输出内容比较冗余, 可以通过 `logger.remove()` 删除默认输出, 然后通过 `logger.add()` 重新配置输出.

```py
import sys
from loguru import logger

logger.remove()                                                                # 删除默认输出
fmt = '[<level>{level: <8}</level>][<green>{time:YYYY-MM-DD HH:mm:ss}</green>]: <level>{message}</level>'
logger.add(sys.stderr,  level='INFO', format=fmt)                              # 重新配置默认输出, level='INFO' 表示输出INFO级别以上的日志

logger.info("this is a info log")
logger.error("this is a error log")
logger.debug("this is a debug log")

[INFO    ][2023-11-23 09:12:40]: this is a info log
[ERROR   ][2023-11-23 09:12:40]: this is a error log
```

logger.add 参数

```py
logger.add(
    sink,                                        # file 对象, logging handle 模块或者字符串; sys.stderr, 'log.txt'
    *,
    level=_defaults.LOGURU_LEVEL,                # 日志输出等级
    format=_defaults.LOGURU_FORMAT,              # 日志格式
    filter=_defaults.LOGURU_FILTER,              # 判断内容是否输出到 sink
    colorize=_defaults.LOGURU_COLORIZE,          # 内容包含颜色标记是否在终端着色
    serialize=_defaults.LOGURU_SERIALIZE,        # 是否将内容转换为 json
    backtrace=_defaults.LOGURU_BACKTRACE,
    diagnose=_defaults.LOGURU_DIAGNOSE,          # 异常跟踪，打印时是否显示变量值
    enqueue=_defaults.LOGURU_ENQUEUE,
    context=_defaults.LOGURU_CONTEXT,
    catch=_defaults.LOGURU_CATCH,

    # 若 sink 为文件时, 可使用以下参数
    rotation:                                    # 设定创建新文件记的条件, 固定时间新建('00:00', '1 week'), 文件到达指定大小新建('500 MB')
    retention:                                   # 日志保留条件, 保留 10 天:'10 days', 保留10个文件: 10
    compression:                                 # 日志文件在关闭时应转换为的压缩或存档格式, 'zip', 'tar.gz'
    delay:                                       # 是在配置 sink 后立即创建文件，还是延迟到第一条记录的消息时再创建, 默认为 False。
    mode:                                        # 内置 open() 函数的打开模式，默认为 a (以追加模式打开文件)
    buffering:                                   # 内置 open() 函数的缓冲策略，默认为1（行缓冲文件）。
    encoding:                                    # 内置 open() 函数的文件编码，如果 None，则默认为 locale.getpreferredencoding()。
    **kwargs
)
```

## 自定义输出

```py
logger.add('report.log', level='INFO', rotation="1 days", retention="1 months")
logger.level("GET", no=21, color="<white>")
logger.level("PUT", no=22, color="<white>")
logger.level("POST", no=23, color="<white>")
logger.add('requests.log', level='GET', filter=lambda r: 20 < r['level'].no < 24, rotation="1 days", retention="1 months")

logger.log("GET", "reques get")
logger.log("PUT", "reques PUT")
logger.log("POST", "reques POST")

[GET     ][2023-12-13 21:42:21]: request get
[PUT     ][2023-12-13 21:47:51]: request put
[POST    ][2023-12-13 21:48:03]: request post
```
