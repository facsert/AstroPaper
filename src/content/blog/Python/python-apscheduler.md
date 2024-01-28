---
author: facsert
pubDatetime: 2023-12-13 21:51:08
title: Python APScheduler
slug: Python APScheduler
featured: false
draft: false
tags:
  - Python
  - APScheduler
description: "Python 定时任务框架 APScheduler"
---

<!--
 * @Author: facsert
 * @Date: 2023-12-13 21:51:08
 * @LastEditTime: 2023-12-13 22:10:40
 * @LastEditors: facsert
 * @Description:
-->

## Table of Contents

APScheduler 是一个 Python 定时任务框架, 支持 Cron、Interval、Date、Timeout 等类型的任务,  
支持分布式任务, 支持任务失败重试, 支持任务并发限制, 支持任务状态监控, 支持任务日志记录

## 安装与介绍

```shell
 $ pip install apscheduler
 $ python -c "import apscheduler" && echo "Installed"
 > Installed
```

apscheduler 有四个基本对象

scheduler: 调度器, 用于调度任务
job: 任务, 定义了任务执行的内容  
trigger: 触发器, 用于定义任务执行的规则  
executor: 执行器, 用于执行任务

## 基本使用

四种调度器:  
BlockingScheduler: 阻塞调度器, 适用于单线程的应用  
BackgroundScheduler: 后台调度, 不影响主线程  
AsyncIOScheduler: 异步IO调度器, 适用于多线程的应用  
GeventScheduler: gevent 调度器, 适用于多线程的应用  

三种触发器:  
cron: 基于 Cron 表达式的触发(周期性)  
interval: 固定间隔触发  
date: 基于日期, 特定时间只触发一次  

```py

def func(name='Jhon'):
    print(f'Hello, world!, {name}')

schedule = schedulers()                          # 选择一种调度器
schedule.add_job(func, 'interval', seconds=5)    # 每 5s 执行一次
scheculer.add_job(func, 'cron', minute='*/5')    # 每 5 分钟执行一次


date = '2024-01-04 12:00:00'                     # 固定时间执行一次
schedule.add_job(func, 'date', run_date=date, args=['lily'])

```

add_job() 方法的参数:  
func: 任务函数  
trigger: 触发器  
args: 任务函数的参数  
kwargs: 任务函数的参数  
id: 任务的唯一标识符  
name: 任务的名称  
misfire_grace_time: 任务失败重试时间  
coalesce: 是否允许任务重复执行  

```python
from apscheduler.schedulers.blocking import BlockingScheduler
from apscheduler.triggers.cron import CronTrigger
from apscheduler.executors.pool import ThreadPoolExecutor

def job_func():
    print('Hello, world!')

scheduler = BlockingScheduler()
scheduler.add_job(job_func, CronTrigger(second='*/5'))
```

## interval 触发器

interval 触发参数:
second: 每秒触发一次  
minute: 每分钟触发一次  
hour: 每小时触发一次  
day: 每天触发一次  
week: 每周触发一次  
month: 每月触发一次  
year: 每年触发一次  

```py
schedule.add_job(func, 'interval', seconds=5)
schedule.add_job(func, 'interval', minute=5)
schedule.add_job(func, 'interval', hour=5)
schedule.add_job(func, 'interval', day=5)
```

## cron 触发器

cron 触发器参数:
year (int|str) – 4-digit year  
month (int|str) – month (1-12)  
day (int|str) – day of month (1-31)  
week (int|str) – ISO week (1-53)  
day_of_week (int|str) – number or name of weekday (0-6 or mon,tue,wed,thu,fri,sat,sun)  
hour (int|str) – hour (0-23)  
minute (int|str) – minute (0-59)  
second (int|str) – second (0-59)  
start_date (datetime|str) – earliest possible date/time to trigger on (inclusive)  
end_date (datetime|str) – latest possible date/time to trigger on (inclusive)  
timezone (datetime.tzinfo|str) – time zone to use for the date/time calculations (defaults to scheduler timezone)  
jitter (int|None) – delay the job execution by jitter seconds at most  

```py
schedule.add_job(func, 'cron', minute='*/5')     
schedule.add_job(func, 'cron', hour=12, minute=30)
schedule.add_job(func, 'cron', day_of_week='fri-sun')
```

## date 触发器

date 触发器参数:
year: 4 位年份  
month: 1-12 月份  
day: 1-31 天  
hour: 0-23 小时  
minute: 0-59 分钟  
second: 0-59 秒

```py
schedule.add_job(func, 'date', run_date='2024-01-08 10:30:00')
```
