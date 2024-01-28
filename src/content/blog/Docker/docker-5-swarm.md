---
author: facsert
pubDatetime: 2023-10-30 11:14:56
title: Docker Cluster
slug: Docker Cluster
featured: false
draft: false
tags:
  - docker
description: "Docker 集群"
---

<!--
 * @Author       : facsert
 * @Date         : 2023-10-30 11:14:56
 * @LastEditTime : 2023-11-06 09:49:51
 * @Description  : edit description
-->

## Table of Contents

```bash
 # master node
 $ docker swarm init                             # 主节点初始化
 > ......
 > docker swarm join --token SWMTKN-1-xxxxxxxxxxxxxxxxxxxxxxx 10.58.14.96:2377
 > ......

 $ docker swarm join-token worker                # 查看工作节点加入命令
 $ docker node ls                                # 查看集群中节点信息
 > ID                            HOSTNAME          STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
 > i98ulk65qim1er06gefq5ihyt     DESKTOP-GMPTFUD   Ready     Active                          24.0.6
 > 4veson0hwfnhs6w8kdu5gnfu2 *   Debian            Ready     Active         Leader           24.0.5
 > a7rf3j9ggr48318bwwxsz6q4s     kwephis           Ready     Active                          24.0.7

 # work node 工作节点加入集群
 $ docker swarm join --token SWMTKN-1-xxxxxxxxxxxxxxxxxxxxxxx 10.58.14.96:2377
```
