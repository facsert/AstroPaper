---
author: facsert
pubDatetime: 2023-04-07  15:28:43
title: Linux crontab
slug: Linux crontab
featured: false
draft: false
tags:
  - Linux
description: "Linux 定时任务"
---

## Table of Contents

```bash
 $ cat /etc/crontab
# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name command to be executed
  17 *  *  *  *   root    cd / && run-parts --report /etc/cron.hourly
  25 6  *  *  *   root    test -x /usr/sbin/anacron || { cd / && run-parts --report /etc/cron.daily; }
```

```bash
 # 列出所有定时任务
 $ crontab -l
 > 0 9 * * * echo hello >> /root/temp.log  # test

 # 编辑定时任务
 $ crontab -e
```
