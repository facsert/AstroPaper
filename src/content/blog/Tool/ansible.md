---
author: facsert
pubDatetime: 2023-06-03 17:44:44
title: Ansible
postSlug: ""
featured: false
draft: false
tags:
  - Ansible
  - Tool
description: "自动化运维工具 Ansible"
---

<!--
 * @Author       : facsert
 * @Date         : 2023-06-03 17:44:44
 * @LastEditTime : 2023-10-11 21:51:31
 * @Description  : edit description
-->

## 介绍

`ansible` 是一个基于 `python` 实现的自动化运维工具, 实现对多个系统进行批量化操作.

## 环境

安装

```bash
 $ apt install ansible
```

### 配置节点

编辑 `/etc/ansible/hosts` 添加群组或节点

```bash
[manager]
localhost

[node]
172.17.0.10

```

## 命令

```bash
 $ ansible
 $ ansible-doc
 $ ansible-playbook
 $ ansible-vault
 $ ansible-console

 $ ansible <host pattern> -m <module> -a <args>
 $ ansilbe all -m shell -a "ifconfig"
```

### 查询

```bash
 $ ansible --version
 $ ansible all --list-host
 $

```

## 执行命令

```bash
 $ ansible <host pattern> -m command -a <command>
 $ ansible <host pattern> -m shell -a <shell command>
```

### 文件

```bash
 $ ansible node -m copy -a "src=/root/local dst=/root/remote"
 $ ansible node -m fetch -a "src=/root/remote dst=/root/local"
```

## Playbook
