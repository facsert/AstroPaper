---
author: facsert
pubDatetime: 2024-01-30 21:12:43
title: Grafana
slug: Grafana
featured: false
draft: false
tags:
  - Prometheus
  - Grafana
description: "Grafana 是一个数据可视化工具"
---

Grafana 是一个强大的可视化平台, 可以集成多种数据工具进行可视化

## Table of Contents

## 介绍

Grafana 是一个数据可视化工具, 可用将某数据源通过图表的方式呈现

## 安装

Grafana 用于展示 Prometheus 采集的监控数据, 通过 promQL 语句绘制图表或使用第三方模板进行数据可视化  
[Grafana Download](https://grafana.com/grafana/download?pg=graf&plcmt=deploy-box-1)
[Grafana Template](https://grafana.com/grafana/dashboards/)

```bash
 $ tar -zxvf grafana-enterprise-9.5.9.linux-arm64.tar.gz
 $ cd grafana-9.5.9 && ./bin/grafana-server
 > ...
 > logger=licensing t=2023-12-07T15:06:25.331289546+08:00 level=info msg="Validated license token" appURL=http://localhost:3000/ source=disk status=NotFound
 > ...
```

配置文件 `conf/default.ini`, 示例 `conf/sample.ini`  
修改 `default_language` 选项为 `detect`(使用浏览器语言)

默认端口是 3000  
初始用户 admin  
初始密码 admin

## 数据源

Grafana 本身无法采集数据, 需要其它工具提供数据

1. 左上角 Menu => Connections => Data source
2. 右上角: Add new data source
3. 选择数据源(prometheus)
4. 配置数据源
   Name: 数据源名称
   Connection: prometheus IP 端口(http://localhost:9090)
   Save: 保存数据源

## Dashboard

使用 Grafana 创建图表, 使用 promQl 筛选数据填入图表  
[Dashboard](https://grafana.com/docs/grafana/latest/dashboards/)  
[Panels and visualizations](https://grafana.com/docs/grafana/latest/panels-visualizations/)

左上角 Menu => Dashboards => 右上角 New
New dashboard: 新建仪表盘
New folder: 新建文件夹
Import: 导入仪表盘模板([Grafana Template](#安装) 下载模板引入)

创建全局变量
dashboard setting => Variables => New variable
Name: 变量名 job
Label: 选择框名称 Job
Query type: Label values  
Label: job
Metric: node_uname_info

Panel options: 仪表盘的标题和描述  
Tooltip: 鼠标悬停表格显示的配置{Tooltip mode: All, Values sort order: Descending}  
Legend: 图表中数据注释显示形式, {Visibility: true, Mode: List, placement: Right}  
Axis: 数轴设置 default  
Graph styles: 图表中数据显示形式(线, 柱状, 散点) {Style: Lines}  
Standard options: 数值设置(单位, 不同数据颜色变化) {Unit: Percent(0.0-1.0), Color scheme: Classic palette}  
Data links: 数据添加外部链接  
Value mappings: 数据固定值固定值替换文本(如 up 值为 0 替换为 OFF) {0: OFF, 1: ON}  
Thresholds: 阈值颜色设定 {base: green, 70: orange, 90:red, Thresholds mode: Percentage}

`$__timeFrom` Grafana 选择的时间起始值 $__timeFrom -> timestamp  
`$**timeTo` Grafana 选择的时间结束值 $**timeTo -> timestamp  
`$__timeFilter` 判断时间点是否在 Grafana 时间段内容 $\_\_timeFilter(timestamp) -> bool
