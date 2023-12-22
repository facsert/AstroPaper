---
author: facsert
pubDatetime: 2023-12-08 21:26:43
title: Prometheus
postSlug: ""
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

- [工具介绍](#工具介绍)  
- [prometheus](#prometheus)  
- [node_exporter](#node_exporter)  
- [grafana](#grafana)  
- [process_exporter](#process_exporter)  
- [loki](#loki)  
- [promtail](#promtail)  
- [alertmanager](#alertmanager)  

## 工具介绍

- Prometheus      : 系统监控和报警系统
- node_exporter   : 节点信息采集工具
- process-exporter: 进程信息采集工具
- loki            : log 聚合系统, 类似于 prometheus
- promtail        : 节点 log 采集工具
- alertmanager    : 报警系统
- grafana         : 数据可视化面板

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
global:                                          # 全局配置
  scrape_interval: 15s                           # 每 15s 收集一次 xx-exporter 的数据
  evaluation_interval: 15s                       # 每隔 15s 计算一次所有规则, 用于告警判断(默认 1m)
  # scrape_timeout: 10s                          # 获取数据的超时时间, 默认 10s

alerting:                                        # Alertmanager 配置
  alertmanagers:
    - static_configs:
        - targets:
          # - alertmanager:9093                  # 配置 alertmanager 服务

rule_files:                                      # 外部告警规则文件
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

## grafana

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

默认端口是 3000
初始用户 admin  
初始密码 admin

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
  alertmanager_url: http://localhost:9093        # 添加告警服务路由
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

## Alertmanager

alertmanager 是一个告警组件, 默认端口 9093

配置告警

```yaml
global:
  resolve_timeout: 5m
  smtp_smarthost: "smtp.com:25"                  # 配置 smtp 服务地址, 邮箱类型和 smtp 服务器对应
  smtp_from: "facsert@outlook.com"               # 发送人
  smtp_auth_username: "facsert@outlook.com"      # 邮箱账户
  smtp_auth_password: "xxxxxx"                   # 邮箱密码
  smtp_require_tls: false

templates:
  - "/root/Desktop/alertmanager/email.tmpl"      # 邮件模板

route:
  receiver: group                                # 默认收件人
  group_by: [...]                                # 分组方式 ['alertname', 'instance'] 不分组 [...], 字段值相同即为通一组, 告警可合并
  group_wait: 30s                                # 等待同组告警时间; 触发告警后, 等待同组告警以合并告警信息发送一次(默认 30s)
  group_interval: 5m                             # 已发送告警后, 同组又出现告警, 再次发送告警的等待时间(默认 5m)
  repeat_interval: 4h                            # 已发送告警, 告警未恢复, 再次发送同样的告警信息的间隔(默认 4h)
  
  # routes:                                      # 设置子路由, 按照路由规则发送, 匹配规则才会发送给接收人
  #   - match:
  #       team: operations
  #     group_by: [env,dc]
  #     receiver: 'ops'
  #   - receiver: ops                            # 路由和标签，根据match来指定发送目标，如果 rule的lable 包含 alertname， 使用 ops 来发送
  #     group_wait: 10s
  #     match:
  #       team: operations

receivers:                                       # 定义接受人群组
  - name: group #
    email_configs:
      - to: "dingwenlong4@huawei.com"            # 如果想发送多个人就以 ','做分割，写多个邮件人即可。
        send_resolved: true
        html: '{{ template "email.default.message" .}}'
        headers:
          from: "Prometheus 警报"
          subject: "Prometheus 告警邮件"
          to: "facsert"

inhibit_rules:
  - source_match:
      severity: "critical"
    target_match:
      severity: "warning"
    equal: ["alertname", "dev", "instance"]
```


group_by: 分组方式, 按 prometheus 告警规则配置文件内的 labels 下的字段值相同为一组  
group_wait: 触发组内第一个告警后, 先不发告警, 等待 group_wait 时间, 看是否有同组告警, 有则合并告警, 仅发送一次  
group_interval: 组内已发送告警后, 同组出现新告警; 先不发, 等待 group_interval 时间, 看是否有同组新告警, 连同已发送信息, 并合并再次发送  
repeat_interval: 已发送告警, 告警一直未复位; 等待 repeat_interval 时间, 再次发送同样的告警


使用 prometheus 监控 node1 node2 node3 机器

```yaml
groups:
- name: Disconnect 
  rules:

  - alert: node1 Disconnect
    expr: sum(up{job="node1"}) == 0              # 告警规则, 表达式成立表示 node1 断连
    for: 1m                                      # 表达式持续成立并持续 1 分钟 pending 时间, 未恢复则开始发送告警
    labels:                                      # 自定义字段, 用于分组
      team: node                                 
      job: disconnect
      severity: critical   
      instance: "192.168.1.10" 
    annotations:
      summary: "node1 disconnect"                #警报描述
      description: "监控节点断连"
      value: "{{ $value }}"

  - alert: node2 Disconnect
    expr: sum(up{job="node2"}) == 0  
    for: 1m  
    labels:                                      # 自定义字段, 用于分组
      team: node
      job: disconnect
      severity: critical   
      instance: "192.168.1.11" 
    annotations:
      summary: "node2 disconnect"                #警报描述
      description: "监控节点断连"
      value: "{{ $value }}"

      ......
```

group_by: ['team`] 使用 `team` 字段分组, 3 条规则 team 字段一致为同一组  
node1 节点表达式成立(expr), 进入 pending 状态; pending 持续 1 分钟(for 字段), 告警发送给 alertmanager, 并进入 firing 状态  
aleertmanager 接到 node1 告警, 等待 30s(group_wait); 期间 node2 触发也告警; 同一组两个告警合并然后发送  
告警发送后 node1 node2 告警未解除, node3 触发告警, 在 node3 触发告警 5m(group_interval) 后发送 node1 node2 node3 合并告警  
node1 node2 node3 告警一直未恢复, 等待 4h(repeat_interval) 后再次发送 node1 node2 node3 合并告警  


邮件模板

```tmpl
{{ define "email.default.message" }}
{{- if gt (len .Alerts.Firing) 0 -}}
<h2> 异常告警 </h2>
<table border="1" bgcolor="#e8e8e8">
  <thead bgcolor="#EF665B">
      <tr bgcolor="#EF665B">
      <td align="center" valign="middle"> 告警主机 </td>
      <td align="center" valign="middle"> 告警级别 </td>
      <td align="center" valign="middle"> 告警状态 </td>
      <td align="center" valign="middle"> 告警类型 </td>
      <td align="center" valign="middle"> 告警概述 </td>
      <td align="center" valign="middle"> 告警取值 </td>
      <td align="center" valign="middle"> 告警时间 </td>
      <td align="center" valign="middle"> 告警详情 </td>
      </tr>
  </thead>
  <tbody>
    {{ range $i, $alert :=.Alerts }}
    <tr>
      <td align="left" valign="middle">{{ $alert.Labels.instance }}</td>
      <td align="left" valign="middle">{{ $alert.Labels.severity }}</td>
      <td align="left" valign="middle">{{   .Status }}</td>
      <td align="left" valign="middle">{{ $alert.Labels.alertname }}</td>
      <td align="left" valign="middle">{{ $alert.Annotations.summary }}</td>
      <td align="left" valign="middle">{{ $alert.Annotations.value }}</td>
      <td align="left" valign="middle">{{ ($alert.StartsAt.Add 28800e9).Format "2006-01-02 15:04:05" }}</td>
      <td align="left" valign="middle">{{ $alert.Annotations.description }}</td>
    </tr>
    {{ end }}
  </tbody>
</table>
{{ end -}}

{{- if gt (len .Alerts.Resolved) 0 -}}
<h2> 异常恢复 </h2>
<table border="1" bgcolor="#e8e8e8">
  <thead bgcolor="#98FB98">
      <tr bgcolor="#98FB98">
      <td align="center" valign="middle"> 告警主机 </td>
      <td align="center" valign="middle"> 告警状态 </td>
      <td align="center" valign="middle"> 告警类型 </td>
      <td align="center" valign="middle"> 告警概述 </td>
      <td align="center" valign="middle"> 告警时间 </td>
      <td align="center" valign="middle"> 恢复时间 </td>
      </tr>
  </thead>
  <tbody>
    {{ range $i, $alert :=.Alerts }}
    <tr>
      <td align="left" valign="middle">{{ $alert.Labels.instance }}</td>
      <td align="left" valign="middle">{{   .Status }}</td>
      <td align="left" valign="middle">{{ $alert.Labels.alertname }}</td>
      <td align="left" valign="middle">{{ $alert.Annotations.summary }}</td>
      <td align="left" valign="middle">{{ ($alert.StartsAt.Add 28800e9).Format "2006-01-02 15:04:05" }}</td>
      <td align="left" valign="middle">{{ ($alert.EndsAt.Add 28800e9).Format "2006-01-02 15:04:05" }}</td>
    </tr>
    {{ end }}
  </tbody>
</table>
{{ end -}}
{{ end -}} 
```

{{ range $i, $alert :=.Alerts }}: 遍历所有告警, 使用 $alert 获取单个告警对象, 使用 . 获取规则配置中 alert 字段下内容
如: $alert.Labels.instance => alert.labels.instance  

```bash
 # 检查配置文件
 $ ./amtool check-config alertmanager-config.yaml
 > Checking '../alertmanager/alertmanager-config.yml'  SUCCESS
 > Found:
 > - global config
 > - route
 > - 1 inhibit rules
 > - 1 receivers
 > - 1 templates
 > SUCCESS

 $ ./alertmanager-linux-amd64 --config.file=alertmanager-config.yaml
```

http://localhost:9093 promtail 界面查看  
重启 alermanager 服务 curl -X POST http://localhost:9093/-/reload alertmanager  
