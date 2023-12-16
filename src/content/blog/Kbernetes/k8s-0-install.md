---
author: facsert
pubDatetime: 2023-10-30 09:59:08
title: Kbernetes Configuration
postSlug: ""
featured: false
draft: false
tags:
  - kbernetes
description: "Kbernetes 配置"
---

<!--
 * @Author       : facsert
 * @Date         : 2023-10-30 09:59:08
 * @LastEditTime : 2023-11-06 21:10:34
 * @Description  : edit description
-->

## Install

安装 `minikube` 和 `kubectl`

[minikube 安装官网](https://minikube.sigs.k8s.io/docs/start/)

```bash
 $ curl -s https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | sudo apt-key add -
 $ echo "deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main" >>/etc/apt/sources.list.d/kubernetes.list

 $ apt update && apt install -y kubelet kubectl kubeadm
 >
```

```bash
 $ uname -m                                      # 查看 CPU 架构
 > x86_64

 $ curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 # x86-64 架构
 $ sudo install minikube-linux-amd64 /usr/local/bin/minikube


 $ curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-arm64 # arm64 架构
 $ sudo install minikube-linux-arm64 /usr/local/bin/minikube


 $ minikube start                                # root 用户添加参数 --force
```
