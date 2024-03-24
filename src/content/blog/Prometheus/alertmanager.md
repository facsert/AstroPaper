---
author: facsert
pubDatetime: 2024-01-30 21:12:43
title: Alertmanager
slug: Alertmanager
featured: false
draft: false
tags:
  - Prometheus
description: "Alertmanager 是 prometheus 的告警管理工具"
---

alertmanager 是一个告警组件, 默认端口 9093

## Table of Contents

## 安装

在 Prometheus 界面下载 [Alertmanager](https://prometheus.io/download/)

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

浏览器打开 http://localhost:9093 界面查看  
重启 alermanager 服务 curl -X POST http://localhost:9093/-/reload

## 配置

配置告警

```yaml
global:
  resolve_timeout: 5m
  smtp_smarthost: "smtp.com:25" # 配置 smtp 服务地址, 邮箱类型和 smtp 服务器对应
  smtp_from: "facsert@outlook.com" # 发送人
  smtp_auth_username: "facsert@outlook.com" # 邮箱账户
  smtp_auth_password: "xxxxxx" # 邮箱密码
  smtp_require_tls: false

templates:
  - "/root/Desktop/alertmanager/email.tmpl" # 邮件模板

route:
  receiver: group # 默认收件人
  group_by: [...] # 分组方式 ['alertname', 'instance'] 不分组 [...], 字段值相同即为通一组, 告警可合并
  group_wait: 30s # 等待同组告警时间; 触发告警后, 等待同组告警以合并告警信息发送一次(默认 30s)
  group_interval: 5m # 已发送告警后, 同组又出现告警, 再次发送告警的等待时间(默认 5m)
  repeat_interval: 4h # 已发送告警, 告警未恢复, 再次发送同样的告警信息的间隔(默认 4h)

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
    webhook_configs: # 发送告警信息给服务器
      url: "http://localhost:8080"

inhibit_rules:
  - source_match:
      severity: "critical"
    target_match:
      severity: "warning"
    equal: ["alertname", "dev", "instance"]
```

group_by: 分组方式, 按 prometheus 告警规则中 labels 的自定义字段  
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

alertmanager 中 group_by 使用 `team` 字段分组, team 字段值一致为同一组  
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
