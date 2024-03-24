---
author: facsert
pubDatetime: 2024-03-12 17:32:16
title: Python datetime
slug: Python datetime
featured: false
draft: false
tags:
  - Python
description: "Python 版本控制工具 datetime"
---

## Table of Contents

## 介绍

datetime 是 python 操作日期和时间的标准库

## datetime

datetime 包含 date time 对象所有信息的时间对象  
datetime 表示一个时间点, 可以与其它 datetime 时间点加减运算

```py
import time
from datetime import datetime

# 自定义时间点
datetime(2024, 3, 18, hour=15, minute=44, second=10)
> datetime.datetime(2024, 3, 18, 15, 44, 10)

# 获取当前时间点
datetime.now()
> datetime.datetime(2024, 3, 18, 15, 22, 27, 706625)

# POSIX 时间戳转 datetime 时间对象, 与 now() 等价
datetime.fromtimestamp(time.time())
> datetime.datetime(2024, 3, 18, 15, 47, 11, 976426)

# datetime 对象按指定格式转换成字符串
datetime.now().strftime("%Y-%m-%d %H:%M:%S")
> '2024-03-18 15:23:38'

# 日期字符串按指定格式转换成 datetime 对象
datetime.strptime('2024-03-18 15:23:38', "%Y-%m-%d %H:%M:%S")
> datetime.datetime(2024, 3, 18, 15, 23, 38)

# 快速打印当前时间点
datetime.now().isoformat()
> '2024-03-18T16:07:10.323165'
```

## fromisoformat

解析任何有效的 ISO 8601 格式的日期字符串

```py
from datetime import datetime

datetime.fromisoformat('2011-11-04')
> datetime.datetime(2011, 11, 4, 0, 0)

datetime.fromisoformat('20111104')
> datetime.datetime(2011, 11, 4, 0, 0)

datetime.fromisoformat('2011-11-04T00:05:23')
> datetime.datetime(2011, 11, 4, 0, 5, 23)

datetime.fromisoformat('2011-11-04T00:05:23Z')
> datetime.datetime(2011, 11, 4, 0, 5, 23, tzinfo=datetime.timezone.utc)

datetime.fromisoformat('20111104T000523')
> datetime.datetime(2011, 11, 4, 0, 5, 23)

datetime.fromisoformat('2011-W01-2T00:05:23.283')
> datetime.datetime(2011, 1, 4, 0, 5, 23, 283000)

datetime.fromisoformat('2011-11-04 00:05:23.283')
> datetime.datetime(2011, 11, 4, 0, 5, 23, 283000)

datetime.fromisoformat('2011-11-04 00:05:23.283+00:00')
> datetime.datetime(2011, 11, 4, 0, 5, 23, 283000, tzinfo=datetime.timezone.utc)

datetime.fromisoformat('2011-11-04T00:05:23+04:00')
> datetime.datetime(2011, 11, 4, 0, 5, 23, tzinfo=datetime.timezone(datetime.timedelta(seconds=14400)))
```

## timedelta

timedelta 表示一段持续的时间, 可用于创建时间片段和日期做加减运算  
`class timedelta(days=0, seconds=0, microseconds=0, milliseconds=0, minutes=0, hours=0, weeks=0)`

```py
from datetime import timedelta

one_hour = timedelta(hours=1)
one_monute = timedelta(minutes=1)

one_hour - one_minute
> datetime.timedelta(seconds=3540)
```

时间点和时间片段支持多种运算

```py
timedelta1 * 2
timedelta1 / 2
timedelta1 - timedelta2
timedelta1 + timedelta2

datetime2 = datetime1 + timedelta
datetime2 = datetime1 - timedelta
timedelta = datetime1 - datetime2

datetime1 == datetime2
timedelta1 == timedelta2
datetime1 != datetime2
timedelta1 != timedelta2

timedelta1 < timedelta2
datetime1 <= datetime2
timedelta1 > timedelta2
datetime1 >= datetime2
```
