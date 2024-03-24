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
from apscheduler.schedulers.background import BackgroundScheduler

def func(name='John'):
    print(f'Hello, world!, {name}')


schedule = BackgroundScheduler()                 # 选择一种调度器
schedule.add_job(func, 'interval', seconds=5)    # 每 5s 执行一次
scheculer.add_job(func, 'cron', minute='*/5')    # 每 5 分钟执行一次


date = '2024-01-04 12:00:00'                     # 固定时间执行一次
schedule.add_job(func, 'date', run_date=date, args=['lily'])
```

| add_job 参数       | 含义                 |
| :----------------- | :------------------- |
| func               | 任务函数             |
| trigger            | 触发器               |
| args               | 任务函数的参数       |
| kwargs             | 任务函数的参数       |
| id                 | 任务的唯一标识符     |
| name               | 任务的名称           |
| misfire_grace_time | 任务失败重试时间     |
| coalesce           | 是否允许任务重复执行 |

```python
from apscheduler.schedulers.blocking import BlockingScheduler

def job_func():
    print('Hello, world!')

scheduler = BlockingScheduler()
scheduler.add_job(job_func, trigger="cron", second='*/5')
```

## cron 触发器

croon 触发器很强大, 支持多种表达式以表示周期或者间隔

|  参数  | `second` |  `minute`  |   `hour`   |  `day`   | `day_of_week` |  `week`  | `month`  |
| :----: | :------: | :--------: | :--------: | :------: | :-----------: | :------: | :------: |
| 时间点 | 秒(0-59) | 分钟(0-59) | 小时(0-23) | 天(1-31) |   每周(0-6)   | 周(1-53) | 月(1-12) |

设置固定时间点触发

```py
from datetime import datetime

timestamp = datetime.strptime("12:30:00", "%H:%M:%S")
schedule.add_job(
  function,
  'cron',
  hours=timestamp.hour,
  minute=timestamp.minute
  secondes=timestamp.second
)
```

设置时间间隔触发

`start1-end1/step1, start2-end2/step2`  
从 `start` 开始到 `end` 结束, 每隔 `step` 触发一次, 使用 `,` 分隔多个表达式

```py
schedule.add_job(func, 'cron', minute='*/5')     # 任意时间, 每隔 5 分钟触发一次
schedule.add_job(func, 'cron', hour='1-6/2')     # 1 点至 6 点, 每隔 2 小时触发一次
schedule.add_job(func, 'cron', day='1, 4, 6')    # 每个月 1, 4, 6 号触发一次
```

## interval 触发器

interval 设置时间间隔触发

| 参数 | `seconds` | `minutes` | `hours` | `days` | `weeks` | `months` |
| :--: | :-------: | :-------: | :-----: | :----: | :-----: | :------: |
| 间隔 |    秒     |   分钟    |  小时   |   天   |   周    |    月    |

```py
from datetime import datetime

timestamp = datetime.strptime("00:00:10", "%H:%M:%S")
schedule.add_job(
  function,
  'interval',
  hours=timestamp.hour,
  minute=timestamp.minute
  secondes=timestamp.second
)

schedule.add_job(func, 'interval', seconds=5)
schedule.add_job(func, 'interval', minute=5)
schedule.add_job(func, 'interval', hour=5)
schedule.add_job(func, 'interval', day=5)
```

注: 时间间隔与时间点有对应关系, 所以用时间点字符串转化为时间间隔

## date 触发器

date 设置一个时间点只执行一次

|  参数  | `second` |  `minute`  |   `hour`   |  `day`   |  `week`  | `month`  |
| :----: | :------: | :--------: | :--------: | :------: | :------: | :------: |
| 时间点 | 秒(0-59) | 分钟(0-59) | 小时(0-23) | 天(1-31) | 周(1-53) | 月(1-12) |

```py
schedule.add_job(func, 'date', run_date='2024-01-08 10:30:00')
```

## 任务存储

创建调度器时可以添加数据库存储任务

```py
from apscheduler.schedulers.blocking import BlockingScheduler
from apscheduler.jobstores.mongodb import MongoDBJobStore
from pymongo import MongoClient


mongo = MongoDBJobStore(                         # 设置 monogo 存储任务
  database='db',
  collection='collection',
  client=MongoClient("mongodb://localhost:27017")
)

schedule = BackgroundScheduler(                  # 使用后台调度
  jobstores={'default': mongo},                  # 使用 mongo 作为默认存储
)

scheduler.start()
```

| 字段 | `_id`  |  `next_run_time`  | `job_state` |
| :--: | :----: | :---------------: | :---------: |
| 含义 | job.id | job.next_run_time |   job状态   |

## 任务调度

```py

from apscheduler.schedulers.background import BackgroundScheduler

def func():
    print(f'Hello, world!')

schedule = BackgroundScheduler()
job = scheduler.add_job(func, 'interval', seconds=2, id="test")

job.pause('test')
job.resume('test')
job.remove('test')

scheduler.pause_job('test')
scheduler.resume_job('test')
scheduler.remove_job('test')

jobs = scheduler.get_jobs()
```

job 属性
id
name
func
args
next_run_time

## 示例

实现一个定时任务调度器, mongo 存储任务

```py
from apscheduler.schedulers.background import BackgroundScheduler
from apscheduler.jobstores.mongodb import MongoDBJobStore


mongo = MongoClient("mongodb://localhost:27017")
db = mongo['database']
Task_Info = db['tasks']

class Schedules:
    """ 定时任务管理 """

    scheduler = None
    jobstores = {
        'default': MongoDBJobStore(
            database='database',
            collection='tasks',
            client=mongo,
        )
    }
    executors = {
        'default': ThreadPoolExecutor(20),
        'processpool': ProcessPoolExecutor(5)
    }
    job_defaults = {
        'coalesce': False,
        'max_instances': 3
    }

    @classmethod
    def init(cls):
        """ 任务管理初始化 """
        cls.scheduler = BackgroundScheduler(
            jobstores=cls.jobstores,
            executors=cls.executors,
            job_defaults=cls.job_defaults,
        )
        cls.scheduler.start()

    @classmethod
    def add_cron_job(cls, id, func, cron_str, params=None):
        """ 执行每天周期性任务 """
        cron = datetime.strptime(cron_str, "%H:%M:%S")
        params = None if params is None else [*params]
        job = cls.scheduler.add_job(
            func,
            'cron',
            id=id,
            args=params,
            hour=cron.hour,
            minute=cron.minute,
            second=cron.second,
            coalesce=True,
            replace_existing=True
        )

        if Task_Info.find_one({'id': id}) is not None:
            return f"task {id} already exist"

        Task_Info.insert_one({
            'id': job.id,
            'function': f"{func.__name__}{job.args}",
            'type': 'cron',
            'time': cron_str,
            'enable': True,
            'description': f"{func.__name__}{job.args} with {cron_str}",
            'start': datetime.now().isoformat(),
        })

        task = Task_Info.find_one({'id': job.id})
        task.pop('_id')
        return task

    @classmethod
    def add_interval_job(cls, id, func, interval_str, params=None):
        """ 执行循环任务 """
        interval = datetime.strptime(interval_str, "%H:%M:%S")
        params = None if params is None else [*params]
        job = cls.scheduler.add_job(
            func,
            'interval',
            id=id,
            args=params,
            hours=interval.hour,
            minutes=interval.minute,
            seconds=interval.second,
            coalesce=True,
            replace_existing=True
        )

        if Task_Info.find_one({'id': id}) is not None:
            return f"task {id} already exist"

        Task_Info.insert_one({
            'id': id,
            'function': f"{func.__name__}{job.args}",
            'type': 'interval',
            'time': interval_str,
            'enable': True,
            'description': f"{func.__name__}{job.args} with {interval_str}",
            'start': datetime.now().isoformat(),
        })

        if Task_Info.find_one({'id': id}) is None:
            return "task already exist"

        task = Task_Info.find_one({'id': job.id})
        task.pop('_id')
        return task

    @classmethod
    def get_job(cls, job_id):
        """ 获取任务信息 """
        return str(cls.scheduler.get_job(job_id))

    @classmethod
    def list_jobs(cls):
        """ 列出所有任务 """
        status = []
        for job in cls.scheduler.get_jobs():
            task = Task_Info.find_one({'id': job.id})
            task.pop('_id')
            status.append({
                **task,
                'next': job.next_run_time,
            })
        return status

    @classmethod
    def delete_job(cls, job_id):
        """ 删除定时任务 """
        cls.scheduler.remove_job(job_id)
        Task_Info.delete_one({'name': job_id})

    @classmethod
    def pause_job(cls, job_id):
        """ 暂停定时任务 """
        cls.scheduler.pause_job(job_id)
        Task_Info.update_one({'name': job_id}, {'$set': {'enable': False}})


    @classmethod
    def resume_job(cls, job_id):
        """ 恢复定时任务 """
        cls.scheduler.resume_job(job_id)
        Task_Info.update_one({'name': job_id}, {'$set': {'enable': True}})
```
