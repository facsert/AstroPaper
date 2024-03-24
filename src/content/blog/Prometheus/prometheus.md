---
author: facsert
pubDatetime: 2023-12-08 21:26:43
title: Prometheus
slug: Prometheus
featured: false
draft: false
tags:
  - Prometheus
  - Tool
description: "自动化运维监控工具 Prometheus"
---

<!--
 * @Author: facsert
 * @Date: 2023-12-08 21:26:43
 * @LastEditTime: 2023-12-10 22:08:28
 * @LastEditors: facsert
 * @Description:
-->

## Table of Contents

## 工具介绍

- Prometheus : 系统监控和报警系统
- node_exporter : 节点信息采集工具
- process-exporter: 进程信息采集工具
- loki : log 聚合系统, 类似于 prometheus
- promtail : 节点 log 采集工具
- alertmanager : 报警系统
- grafana : 数据可视化面板

## Prometheus

数据监控, 采集, 告警系统; 主动采集被测节点信息  
[Prometheus Download](https://prometheus.io/download/)

```bash
 # 解压安装包, 创建数据目录, 创建配置文件
 $ tar -zxvf prometheus-2.45.1.linux-amd64.tar.gz
 $ cd prometheus-2.45.1.linux-amd64 && mkdir -p data
 $ vi prometheus.yml
```

官方默认配置文件

```yaml
global: # 全局配置
  scrape_interval: 15s # 每 15s 收集一次 xx-exporter 的数据
  evaluation_interval: 15s # 每隔 15s 计算一次所有规则, 用于告警判断(默认 1m)
  # scrape_timeout: 10s                          # 获取数据的超时时间, 默认 10s

alerting: # Alertmanager 配置
  alertmanagers:
    - static_configs:
        - targets:
          # - alertmanager:9093                  # 配置 alertmanager 服务

rule_files:# 外部告警规则文件
  # - "first_rules.yml"
  # - "second_rules.yml"

scrape_configs:
  - job_name: "prometheus"
    static_configs:
      - targets: ["localhost:9090"]

  # 添加外部采集服务, 如 node_exporter process-exporter
  # - job_name: "node_exporter"
  #   static_configs:
  #     - targets: ["localhost:9100"]
```

默认端口: 9090

```bash
 # 检查配置文件
 $ ./promtool check config prometheus.yml
 > Checking prometheus.yml
 > SUCCESS: prometheus.yml is valid prometheus config file syntax

 # 启动 prometheus
 $ ./prometheus --config.file=./prometheus.yml --web.enable-lifecycle
 > ts=2023-12-07T09:13:17.909Z caller=main.go:1043 level=info msg="TSDB started"
 > ts=2023-12-07T09:13:17.909Z caller=main.go:1224 level=info msg="Loading configuration file" filename=prometheus.yml
 > ts=2023-12-07T09:13:17.909Z caller=main.go:1261 level=info msg="Completed loading of configuration file" filename=prometheus.yml
 > ts=2023-12-07T09:13:17.910Z caller=main.go:1004 level=info msg="Server is ready to receive web requests."
```

注意: 添加 `--web.enable-lifecycle` 启用热重载, 执行 `curl -X POST http://localhost:9090/-/reload` 重载  
浏览器打开 `http://localhost:9000` 打开 prometheus 控制台

## node_exporter

在监控节点运行 node_exporter 采集节点信息, 待 peometheus 收集  
[node_exporter Download](https://prometheus.io/download/)

```bash
 $ tar -zxvf node_exporter-1.7.0.linux-arm64.tar.gz
 $ cd node_exporter-1.7.0.linux-arm64
 $ cp node_exporter /usr/local/bin

 $ ./node_exporter
 > ts=1970-02-06T18:49:59.679Z caller=tls_config.go:274 level=info msg="Listening on" address=0.0.0.0:9100
 > ts=1970-02-06T18:49:59.679Z caller=tls_config.go:277 level=info msg="TLS is disabled." http2=false address=0.0.0.0:9100

 # 自定义 HOST 和 端口
 $ ./node_exporter --web.listen-address 127.0.0.1:8080

 # 指定配置文件
 $ ./node_exporter --web.config.file="config.yaml"
```

浏览器打开 `http://localhost:9100` 进入 node_exporter 控制台  
Prometheus 配置文件添加 node_export 监控, 重载 Prometheus  
Prometheus 根据节点信息获取 node_exporter 采集的监控数据

```yaml
scrape_configs:
  ...
  ...

  - job_name: "node_export"
    static_configs:
      - targets: ["<节点 HOST>:9100"]
```

## process_exporter

process_exporter 用于监控进程和线程等更细致的信息  
[process-exporter](https://github.com/ncabatoff/process-exporter)

```bash
 # 解压包, 创建配置文件
 $ tar -zxvf process-exporter-0.7.9.linux-arm64.tar.gz
 $ cd process-exporter-0.7.9.linux-arm64
 $ vi config.yaml
```

配置需要监控的进程

```yaml
process_names:
  - name: "{{.Matches}}"
    cmdline:
      - "sshd"

  - name: "{{.Matches}}"
    cmdline:
      - "python"

  - name: "{{.Matches}}"
    cmdline:
      - "docker"
```

```bash
 $ nohup ./process-exporter -config.path config.yaml &
```

打开浏览器 `http://localhost:9256/metrics`  
若系统种存在监控的进程, log 必定出现 `cpu_seconds_total` 字段

```bash
namedprocess_namegroup_cpu_seconds_total{groupname="map[:sshd]",mode="system"} 0.19000000000000128
namedprocess_namegroup_cpu_seconds_total{groupname="map[:sshd]",mode="user"} 0.009999999999999787
namedprocess_namegroup_cpu_seconds_total{groupname="map[:python]",mode="system"} 0.010000000000019327
namedprocess_namegroup_cpu_seconds_total{groupname="map[:python]",mode="user"} 0.009999999999990905
```

Prometheus 配置文件添加 node_export 监控, 重启 Prometheus

```yaml
scrape_configs:
   ...
   ...

  - job_name: "process_exporter"
    static_configs:
      - targets: ["<节点 HOST>:9256"]
```

Prometheus `http://localhost:9090/service-discovery?search=` 查询所有监控的服务

## loki

Loki 是一个模仿 Prometheus 的日志聚合系统, 也可以使用 Grafana 作为展示界面
[Github Loki](https://github.com/grafana/loki/releases/)

官方配置

```yaml
auth_enabled: false

server:
  http_listen_port: 3100 # 对外接口
  grpc_listen_port: 9096

common:
  path_prefix: /tmp/loki
  storage:
    filesystem:
      chunks_directory: /tmp/loki/chunks
      rules_directory: /tmp/loki/rules
  replication_factor: 1
  ring:
    instance_addr: 127.0.0.1
    kvstore:
      store: inmemory

schema_config:
  configs:
    - from: 2020-10-24
      store: boltdb-shipper
      object_store: filesystem
      schema: v11
      index:
        prefix: index_
        period: 24h

ruler:
  alertmanager_url: http://localhost:9093 # 添加告警服务路由
# By default, Loki will send anonymous, but uniquely-identifiable usage and configuration
# analytics to Grafana Labs. These statistics are sent to https://stats.grafana.org/
#
# Statistics help us better understand how Loki is used, and they show us performance
# levels for most users. This helps us prioritize features and documentation.
# For more information on what's sent, look at
# https://github.com/grafana/loki/blob/main/pkg/usagestats/stats.go
# Refer to the buildReport method to see what goes into a report.
#
# If you would like to disable reporting, uncomment the following lines:
#analytics:
#  reporting_enabled: false
```

创建配置文件, 拉起 loki 服务, 默认端口 3100

```bash
 $ ./loki-linux-amd64 -config.file=$PWD/loki-config.yaml
```

## promtail

[Github Promtail](https://github.com/grafana/loki/releases/)

```yaml
server:
  http_listen_port: 9080 # 对外暴露端口
  grpc_listen_port: 0

positions:
  filename: /tmp/positions.yaml

clients: # 机器需要与 loki 服务通信
  - url: http://193.168.1.123:3100/loki/api/v1/push # url 需与 loki 路由和端口一致

scrape_configs:
  - job_name: panic
    static_configs:
      - targets:
          - localhost
        labels:
          job: ald_panic # 在 grafana 显示的 job
          __path__: /proc/kbox/regions/panic # 检测文件

  - job_name: messages
    static_configs:
      - targets:
          - localhost
        labels:
          job: ald_msg # 在 grafana 显示的 job
          __path__: /var/log/messages # 检测文件
```

创建配置文件 promtail-config.yaml, 填入以上内容, 拉起 promtail 服务, 默认端口 9080

```bash
 $ ./promtail-linux-amd64 -config.file=$PWD/config.yaml
```

http://localhost:9080 promtail 界面查看
