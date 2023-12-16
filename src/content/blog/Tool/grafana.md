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

## 性能监控

node_exporter 数据收集
prometheus 数据处理和监控
grafana 数据可视化

### Prometheus

[Prometheus Download](https://prometheus.io/download/)

```bash
 $ tar -zxvf prometheus-2.45.1.linux-amd64.tar.gz
 $ cd prometheus-2.45.1.linux-amd64 && mkdir -p data
 $ vi prometheus.yml
```

官方默认配置文件

```yaml
# my global config
global:
  scrape_interval: 15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

# Alertmanager configuration
alerting:
  alertmanagers:
    - static_configs:
        - targets:
          # - alertmanager:9093

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  # - "first_rules.yml"
  # - "second_rules.yml"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: "prometheus"

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
      - targets: ["localhost:9090"]
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

config.file: 指定配置文件
web.enable-lifecycle: 热重载, 修改配置文件后, 使用 http 请求重载(`curl -X POST http://localhost:9090/-/reload`)

浏览器打开 `http://localhost:9000`

### node_exporter

[node_exporter Download](https://prometheus.io/download/)

```bash
 $ tar -zxvf node_exporter-1.7.0.linux-arm64.tar.gz
 $ cd node_exporter-1.7.0.linux-arm64
 $ cp node_exporter /usr/local/bin

 $ ./node_exporter
 > ts=1970-02-06T18:49:59.679Z caller=tls_config.go:274 level=info msg="Listening on" address=0.0.0.0:9100
 > ts=1970-02-06T18:49:59.679Z caller=tls_config.go:277 level=info msg="TLS is disabled." http2=false address=0.0.0.0:9100
```

浏览器打开 `http://localhost:9100`

Prometheus 配置文件添加 node_export 监控, 重启 Prometheus

```yaml
scrape_configs:
  ...
  ...

  - job_name: "node_export"
    static_configs:
      - targets: ["localhost:9100"]
```

### grafana

[Grafana Download](https://grafana.com/grafana/download?pg=graf&plcmt=deploy-box-1)

选择平台和链接下载包

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

## 进程监控

### process_exporter

[process-exporter](https://github.com/ncabatoff/process-exporter)
下载对应版本包, 将包放入被测机器

```bash
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

```log
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
      - targets: ["localhost:9256"]
```

Prometheus `http://localhost:9090/service-discovery?search=` 查询所有监控的服务

## log 监控

### loki

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
  alertmanager_url: http://localhost:9093
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

### promtail

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

curl -X POST http://localhost:9090/-/reload prometheus

## Alertmanager

alertmanager 是一个告警组件, 默认端口 9093

配置告警

```yaml
global:
  resolve_timeout: 5m
  smtp_smarthost: "smtp.com:25" # 配置 smtp 服务地址, 邮箱类型和 smtp 服务器对应
  smtp_from: "facsert@outlook.com" #
  smtp_auth_username: "facsert@outlook.com" # 邮箱账户
  smtp_auth_password: "xxxxxx" # 邮箱密码
  smtp_require_tls: false

templates:
  - "/root/Desktop/alertmanager/email.tmpl" # 邮件模板

route:
  receiver: group # 默认收件人
  group_wait: 3ms # 在组内等待所配置的时间，如果同组内，30秒内出现相同报警，在一个组内出现。
  group_interval: 5m # 如果组内内容不变化，合并为一条警报信息，5m后发送。
  repeat_interval: 24h # 发送报警间隔，如果指定时间内没有修复，则重新发送报警。
  group_by: [...] # 报警分组 ['alertname', 'instance'] 不分组 [...]

  # routes:                                      # 设置子路由, 按照路由规则发送, 匹配规则才会发送给接收人
  #   - match:
  #       team: operations
  #     group_by: [env,dc]
  #     receiver: 'ops'
  #   - receiver: ops                            # 路由和标签，根据match来指定发送目标，如果 rule的lable 包含 alertname， 使用 ops 来发送
  #     group_wait: 10s
  #     match:
  #       team: operations

receivers: # 定义接受人群组
  - name: group #
    email_configs:
      - to: "dingwenlong4@huawei.com" # 如果想发送多个人就以 ','做分割，写多个邮件人即可。
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

邮件模板

```tmpl
{{ define "email.default.message" }}
{{- if gt (len .Alerts.Firing) 0 -}}{{ range $i, $alert :=.Alerts }}
{{- if eq $alert.Labels.severity "紧急" }}
========紧急告警==========<br>
{{- else }}

{{- end }}
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
    <tr>
      <td align="left" valign="middle">{{ .Labels.instance }}</td>
      <td align="left" valign="middle">{{ $alert.Labels.severity }}</td>
      <td align="left" valign="middle">{{   .Status }}</td>
      <td align="left" valign="middle">{{ $alert.Labels.alertname }}</td>
      <td align="left" valign="middle">{{ $alert.Annotations.summary }}</td>
      <td align="left" valign="middle">{{ $alert.Annotations.value }}</td>
      <td align="left" valign="middle">{{ ($alert.StartsAt.Add 28800e9).Format "2006-01-02 15:04:05" }}</td>
      <td align="left" valign="middle">{{ $alert.Annotations.description }}</td>
    </tr>
  </tbody>
</table>

{{ end }}
{{ end -}}
{{- if gt (len .Alerts.Resolved) 0 -}}{{ range $i, $alert :=.Alerts }}

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
    <tr>
      <td align="left" valign="middle">{{ .Labels.instance }}</td>
      <td align="left" valign="middle">{{   .Status }}</td>
      <td align="left" valign="middle">{{ $alert.Labels.alertname }}</td>
      <td align="left" valign="middle">{{ $alert.Annotations.summary }}</td>
      <td align="left" valign="middle">{{ ($alert.StartsAt.Add 28800e9).Format "2006-01-02 15:04:05" }}</td>
      <td align="left" valign="middle">{{ ($alert.EndsAt.Add 28800e9).Format "2006-01-02 15:04:05" }}</td>
    </tr>
  </tbody>
</table>

{{ end }}
{{ end -}}
{{ end }}
```

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
