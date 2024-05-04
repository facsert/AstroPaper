---
author: facsert
pubDatetime: 2024-01-30 21:12:43
title: PromQl
slug: PromQl
featured: false
draft: false
tags:
  - Prometheus
description: "PromQl 是 prometheus 用具数据筛选的语言"
---

## Table of Contents

## 介绍

PromQL 是 prometheus 内置的数据查询语言, 使用 PromQL 对 prometheus 收集的数据进行筛选计算等  
prometheus 数据格式: `data_name{label0=value, label1=value, label2=value} data_value`

```bash
# Node exporter 收集的 metrics 数据示例

# HELP node_cpu_seconds_total Seconds the CPUs spent in each mode.
# TYPE node_cpu_seconds_total counter
node_cpu_seconds_total{cpu="0",mode="idle"} 24226.24
node_cpu_seconds_total{cpu="0",mode="iowait"} 0.96
node_cpu_seconds_total{cpu="0",mode="irq"} 0
node_cpu_seconds_total{cpu="0",mode="nice"} 0

# HELP node_memory_MemFree_bytes Memory information field MemFree_bytes.
# TYPE node_memory_MemFree_bytes gauge
node_memory_MemFree_bytes 9.904422912e+09

# HELP node_memory_Active_bytes Memory information field Active_bytes.
# TYPE node_memory_Active_bytes gauge
node_memory_Active_bytes 4.65158144e+08
```

使用 `data_name{label0=value, label1=value,}` 筛选数据

```bash
# 显示所有的 node_cpu_seconds_total 数据中 cpu="0" 的数据(不同节点的信息会附带 instance 和 job)
node_cpu_seconds_total{cpu="0"}
> node_cpu_seconds_total{cpu="0", instance="10.143.232.175:9100", job="V2-Node", mode="idle"}    26274.44
> ......
> node_cpu_seconds_total{cpu="0", instance="10.39.104.158:9100", job="V2-Node", mode="system"}   392.63
> ......
> node_cpu_seconds_total{cpu="0", instance="10.39.104.18:9100", job="V2-Node", mode="steal"}     0
```

## metrics 数据类型

Prometheus 的 metrics 数据有多种类型, 数据注释结尾中会表明数据类型  
prometheus 的数据本质是 /proc 下文件内容, /proc 记录着节点, 进程 等的实时数据  
ex: `/proc/stat (cpu 实时数据)  /proc/meminfo (内存实时数据)  /proc/<pid> (进程实时数据)`

### Counter

计数器类型, 数值只增加(除非系统重置), 如 node_cpu_seconds_total 节点 cpu 执行时间

```bash
# HELP node_cpu_seconds_total Seconds the CPUs spent in each mode.
# TYPE node_cpu_seconds_total counter
node_cpu_seconds_total{cpu="0",mode="idle"} 31712.24
node_cpu_seconds_total{cpu="0",mode="iowait"} 0.96
node_cpu_seconds_total{cpu="0",mode="irq"} 0
node_cpu_seconds_total{cpu="0",mode="nice"} 0
node_cpu_seconds_total{cpu="0",mode="softirq"} 119.36
node_cpu_seconds_total{cpu="0",mode="steal"} 0
node_cpu_seconds_total{cpu="0",mode="system"} 617.85
node_cpu_seconds_total{cpu="0",mode="user"} 1529.83
```

### Gauge

可增减的仪表盘, 数值可增可减, 用于表示当前状态, node_memory_MemFree_bytes 当前空闲内存

```bash
# HELP node_memory_MemFree_bytes Memory information field MemFree_bytes.
# TYPE node_memory_MemFree_bytes gauge
node_memory_MemFree_bytes 9.904422912e+09
# HELP node_memory_MemTotal_bytes Memory information field MemTotal_bytes.
# TYPE node_memory_MemTotal_bytes gauge
node_memory_MemTotal_bytes 2.4360300544e+10
# HELP node_memory_Mlocked_bytes Memory information field Mlocked_bytes.
# TYPE node_memory_Mlocked_bytes gauge
node_memory_Mlocked_bytes 0
```

### Histogram

直方图, 表示一组观测值的分布, 通常用于度量请求持续时间等

```bash
# HELP http_request_duration_seconds HTTP request duration in seconds
# TYPE http_request_duration_seconds histogram
http_request_duration_seconds_bucket{le="0.1"} 10
http_request_duration_seconds_bucket{le="1"} 20
http_request_duration_seconds_bucket{le="5"} 30
http_request_duration_seconds_bucket{le="10"} 40
http_request_duration_seconds_bucket{le="+Inf"} 50
http_request_duration_seconds_sum 235.5
http_request_duration_seconds_count 50
```

操作次数 50, 耗时 235.5s  
耗时 小于 0.1s 的有 10次  
耗时 小于 1s 的有 20次  
耗时 小于 5s 的有 30次  
耗时 小于 10s 的有 40次  
耗时 小于 无穷大的有 50次

### Summary

摘要, 类似于 Histogram，但是它会保留一系列指定的分位数

```bash
# HELP prometheus_tsdb_wal_fsync_duration_seconds Duration of WAL fsync.
# TYPE prometheus_tsdb_wal_fsync_duration_seconds summary
prometheus_tsdb_wal_fsync_duration_seconds{quantile="0.5"} 0.012352463
prometheus_tsdb_wal_fsync_duration_seconds{quantile="0.9"} 0.014458005
prometheus_tsdb_wal_fsync_duration_seconds{quantile="0.99"} 0.017316173
prometheus_tsdb_wal_fsync_duration_seconds_sum 2.888716127000002
prometheus_tsdb_wal_fsync_duration_seconds_count 216
```

操作的总次数为216次，耗时2.888716127000002s, 按耗时给所有次数排序  
中位数（quantile=0.5）的耗时为0.012352463  
9分位数（quantile=0.9）的耗时为0.014458005s

## 匹配数据

Prometheus 采集的数据有类别和时间两个重要属性, 以空间坐标轴比喻  
x 轴的点为不同的时间, y 轴上不同点表示不同类别, z 轴表示数据的值

使用 PromQL 查询返回的数据有 4 种类型

- Instan vector: **瞬时向量**, 某个时间点采集的数据集合
- Range vector: **区间向量**, 指定时间范围内, 无数瞬时向量的集合
- Scalar: **标量**, 浮点数字, 无时间类别属性
- String: **字符串**

## 匹配

- 完全匹配: 等于`=`, 不等于 `!=`
- 正则匹配: 正向 `=~` 反向 `!~`

```bash
# cpu0
node_cpu_seconds_total{cpu="0"}

# 非 cpu0
node_cpu_seconds_total{cpu!="0"}

# cpu0 job 中包含 V2
node_cpu_seconds_total{cpu="0",job=~".*V2.*"}

# cpu0 job 中包含 V2
node_cpu_seconds_total{cpu="0",job!~".*V2.*"}
```

## 范围查询

上述查询的结果都是瞬时值, 是从最新的数据查询, 这样的结果称为**瞬时向量**  
实际上, prometheus 获取的数据是带有时间的, 一段时间内的数据称作**区间向量**

```bash
# 在瞬时表达式结尾添加 [time] 表示从当前往前一段时间的数据
node_cpu_seconds_total{cpu="0", instance="10.143.232.175:9100", mode="idle"}[5m]
> node_cpu_seconds_total{cpu="0", instance="10.143.232.175:9100", mode="idle"}  34848.9 @1708573601.411
>                                                                               34863.28 @1708573616.411
>                                                                               34877.63 @1708573631.411
>                                                                               34892 @1708573646.411
>                                                                               34906.3 @1708573661.411
>                                                                               34920.6 @1708573676.411
>                                                                               34934.85 @1708573691.411
>                                                                               34949.08 @1708573706.411
>                                                                               34963.29 @1708573721.411
>                                                                               34977.53 @1708573736.411
>                                                                               34991.82 @1708573751.411
>                                                                               35006.06 @1708573766.411
>                                                                               35020.08 @1708573781.411
>                                                                               35034.16 @1708573796.411
>                                                                               35048.44 @1708573811.411
```

| 符号 |  `s`   |   `m`    |   `h`    |  `d`   |  `w`   |  `y`   |
| :--: | :----: | :------: | :------: | :----: | :----: | :----: |
| 含义 | 秒[5s] | 分钟[5m] | 小时[5h] | 天[5d] | 周[5w] | 年[5y] |

默认查询都是以当前时间作为基准, 取当前时间点或者, 当前往前推一段时间  
若要更换时间基准, 如 1 天前同一时刻, 或者昨天一整天的数据, 使用 **offset** 切换时间基准

```bash
# 当前时间往前推 1 天
node_cpu_seconds_total{cpu="0", instance="10.143.232.175:9100", mode="idle"} offset 1d

# 当前是 02/22 10:00, 先往前推 10 小时到 00:00, 再取 00:00 往前 1 天的数据
node_cpu_seconds_total{cpu="0", instance="10.143.232.175:9100", mode="idle"}[1d] offset 10h
```

## 聚合操作

对瞬时向量进行聚合操作

- sum: 求和 sum(node_cpu_seconds_total{cpu="0"})
- min: 最小值 min(node_cpu_seconds_total{cpu="0"})
- max: 最大值 max(node_cpu_seconds_total{cpu="0"})
- avg: 平均值 avg(node_cpu_seconds_total{cpu="0"})
- count: 计数 count(node_cpu_seconds_total{cpu="0"})
- topk: 前 n 条数据 topk(5, node_cpu_seconds_total{cpu="0"})
- bottomk: 最后 n 条数据 bottomkk(5, node_cpu_seconds_total{cpu="0"})
- stddev: 标准差

## 数学运算

瞬时向量可以直接与标量计算, 如 Memory 原始数据单位是 Byte, 除以 1024, 单位变成 KB
瞬时向量和瞬时向量运算, 只有 `{label0=value, label1=value}` 完全一致才会计算  
使用 `on` 或 `ignore` 让不同标签的向量也能做数学运算

```bash
node_cpu_seconds_total  +  on(instance, job, mode, cpu) node_cpu_guest_seconds_total
```

## 内置函数

### increase

计算一个 Counter 数据的增长, 取最后时刻的数据减去最初时刻的数据  
`increase(range-vector)`

```bash
increase(node_cpu_seconds_total{cpu="0", instance="10.143.232.175:9100", job="V2-Node",mode="idle"}[5m])
> {cpu="0", instance="10.143.232.175:9100", job="V2-Node", mode="idle"}    	218.2709700476184
```

### rate

计算平均每秒的增长, 即增长量除以时间
`rate(range-vector) == increase(range-vector) / time`

```bash
rate(node_cpu_seconds_total{cpu="0", instance="10.143.232.175:9100", job="V2-Node",mode="idle"}[5m])
> {cpu="0", instance="10.143.232.175:9100", job="V2-Node", mode="idle"}    		0.7518475626666798
```

### irate

计算最后两组数据的增长率(瞬时变化): 最后的数据 - 最后前一次数据 / 两个数据时间差

```bash
# 由于仅取最后两组数据, 在同一个时间点, [2m] [5m] (无论往前推多少时间)的最后两组数据一致, 结果也一致
irate(node_cpu_seconds_total{cpu="0", instance="10.143.232.175:9100", job="V2-Node",mode="idle"}[2m])
> {cpu="0", instance="10.143.232.175:9100", job="V2-Node", mode="idle"}       0.8286666666666861
```

## 示例

### 节点连接

```bash
# job 中包含 Node 的(1 连接, 0 断连)
up{job=~".*Node"}
up{instance="10.121.238.42:6100", job="V2-Node"}     1
up{instance="10.121.238.42:7100", job="V2-Node"}     1
up{instance="10.143.232.175:9100", job="V2-Node"}    1
up{instance="10.144.226.244:9100", job="V1-Node"}    1
```

### CPU 占用率

/proc/stat 保存 CPU 执行时间数据([Counter](#counter)类型)  
CPU 占用: 一段时间内, CPU 工作时间(总时间 - 空闲时间)占总时间的比值

| 字段 | `user`     | `nice`             | `system`   | `idle`     | `iowait`    | `irq`      | `softirq`  | `steal/guest/guest_nice` |
| :--: | :--------- | :----------------- | :--------- | :--------- | :---------- | :--------- | :--------- | :----------------------- |
|  值  | `1128155`  | `0`                | `990765`   | `52801349` | `8147`      | `0`        | `5547`     | `0/0/0`                  |
| 含义 | 用户态时间 | 低优先级用户态时间 | 内核态时间 | 空闲时间   | I/O等待时间 | 硬中断时间 | 软中断时间 | 虚拟机占用时间           |

`CPU Used = 1 - (idle / (user + nice + system + idle + iowait + irq + softirq + steal + guest + guest_nice))`

```bash
# 12m 最后两组数据计算平均增长率
irate(node_cpu_seconds_total{mode="idle"}[2m])
irate(node_cpu_seconds_total[2m])

# 1 减去空闲百分比获得 CPU 占用百分比(按)
1 - (sum by(instance) (irate(node_cpu_seconds_total{mode="idle"}[2m])) / sum by(instance) (irate(node_cpu_seconds_total[2m])))
```

```bash
# 注: sum by 语句后仅保留设置的索引, 类似于 pandas 的 groupby
sum by(job,instance) (node_cpu_seconds_total{mode="idle"})
{instance="10.121.238.42:1100", job="V2-Node"}        192876.24
{instance="10.121.238.42:2100", job="V2-Node"}        1113100.96
{instance="10.121.238.42:6100", job="V2-Node"}        1156084.41
{instance="10.121.238.42:7100", job="V2-Node"}        1154130.68
```

### 内存占用

/proc/meminfo 保存 Memory 数据([Gauge](#gauge)类型)  
Memory Used: (MemTotal - MemAvailable) / MemTotal

```bash
# 已使用内存(单位 Byte)
node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes

# 已使用内存(单位 GB)
(node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes) / 1024 / 1024 / 1024

# 内存占用百分比
(node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes) /  node_memory_MemTotal_bytes
```

### 硬盘占用

Disk Used: 1- (Disk Available / Disk Total)

```bash
# 可用空间
node_filesystem_avail_bytes

# 硬盘总空间
node_filesystem_size_bytes

1 - ((node_filesystem_avail_bytes) /  node_filesystem_size_bytes)
```
