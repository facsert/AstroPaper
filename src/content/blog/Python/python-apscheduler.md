---
author: facsert
pubDatetime: 2023-12-13 21:51:08
title: Python APScheduler
postSlug: ""
featured: true
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

```python

```
