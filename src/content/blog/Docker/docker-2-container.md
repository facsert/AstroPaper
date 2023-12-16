---
author: facsert
pubDatetime: 2022-10-10 15:28:43
title: Docker Container
postSlug: ""
featured: false
draft: false
tags:
  - docker
description: "Docker 容器"
---

<!--
 * @Author       : facsert
 * @Date         : 2022-10-10 15:28:43
 * @LastEditTime : 2023-11-03 15:26:09
 * @Description  : edit description
-->

## 容器列表

```bash
 $ docker ps [OPTIONS]                                                         # 显示容器列表及相关信息

 $ docker ps
 $ docker ps -a, --all                                                         # 显示所有容器, 包括未运行的
 $ docker ps -s, --size                                                        # 显示正在运行的容器, 显示容器大小
 $ docker ps -q, --quite                                                       # 仅显示容器 ID

CONTAINER ID   IMAGE         COMMAND                  CREATED        STATUS         PORTS                     NAMES      SIZE
7eab1cd126af   43761bd5b76d  "docker-entrypoint.s…"   43 hours ago   Up 43 hours    27017/tcp                 person     0B (virtual 700MB)
ee0c15a3a0ee   mongo         "docker-entrypoint.s…"   44 hours ago   Up 44 hours    0.0.0.0:8589->27017/tcp   demo       0B (virtual 700MB)
```

| `CONTAINER ID` | `IMAGE`        | `COMMAND`            | `CREATED`    | `STATUS` | `PORTS`            | `NAMES`  | `SIZE`   |
| :------------- | :------------- | :------------------- | :----------- | :------- | :----------------- | :------- | :------- |
| 容器 ID        | 生成容器的镜像 | 启动容器时运行的命令 | 容器创建时间 | 容器状态 | 机器端口->容器端口 | 容器名称 | 容器大小 |

```bash
 $ docker stop    <CONTAINER ID | NAMES>                                       # 关闭容器
 $ docker start   <CONTAINER ID | NAMES>                                       # 启动容器
 $ docker kill    <CONTAINER ID | NAMES>                                       # 杀死容器进程
 $ docker restart <CONTAINER ID | NAMES>                                       # 重启容器
 $ docker rm -f   <CONTAINER ID | NAMES>                                       # 强制删除容器(运行中的容器也会删除)

 $ docker logs    <CONTAINER ID | NAMES>                                       # 显示指定容器 log
 $ docker top     <CONTAINER ID | NAMES>                                       # 列出指定容器进程
 $ docker port    <CONTAINER ID | NAMES>                                       # 显示容器的端口映射

 $ docker exec [OPTIONS] CONTAINER COMMAND [ARG...]                            # 进入容器执行命令(容器在运行状态才可进入)
 $ docker exec -it mongo-slave1 bash                                           # 以交互式进入 mongo-slave1 容器,执行 bash, 并留在容器中(exit 退出容器)
 $ docker exec mongo-slave1 mongosh --version                                  # 进入 mongo-slave1 容器查看 mongosh 版本并退出
 > 1.6.1
```

## 创建容器

```bash
Usage: docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

Options:
  -d, --detach                                                                 # 后台运行容器，并输出容器ID
  -e, --env list                                                               # 设置环境变量，该变量可以在容器内使用
  -h, --hostname string                                                        # 指定容器的hostname
  -i, --interactive                                                            # 以交互模式运行容器，通常与-t同时使用
  -l, --label list                                                             # 给容器添加标签
  --name <name>                                                                # 设置容器名称，否则会自动命名
  --network string                                                             # 将容器加入指定网络
  -p, --publish list                                                           # 设置容器映射端口 <server port>:<docker port>
  -P,--publish-all                                                             # 将容器设置的所有exposed端口进行随机映射
  --restart <restart option>                                                   # 容器重启策略，默认为不重启
    on-failure[:max-retries]：                                                 # 在容器非正常退出时重启，可以设置重启次数。
    unless-stopped：                                                           # 总是重启，除非使用stop停止容器
    always：                                                                   # 总是重启
  --rm                                                                         # 容器退出时则自动删除容器
  -t, --tty                                                                    # 分配一个伪终端
  -u, --user <username>                                                        # 运行用户或者UID
  -v, --volume list                                                            # 容器目录映射到主机目录 <server path>:<docker path>
  -w, --workdir <work path>                                                    # 容器的工作目录
  --privileged                                                                 # 给容器特权
```

```bash
 $ docker run -it --rm ubuntu bash                                             # 以 ubuntu 镜像创建容器(自动命名), 创建后执行 bash, 退出后自动删除容器
 $ docker run -itd -p 8589:27017 --name demo mongo                             # 以 mongo 镜像创建 demo 容器, 后台运行, 机器 8589 端口映射到容器 27017 端口
 $ docker run -it --rm -v /root/docker:/root debian bash                       # debian 镜像创建容器, 容器 /root 目录映射到主机 /root/docker 目录

$ docker ps
CONTAINER ID  IMAGE  COMMAND                 CREATED         STATUS         PORTS                     NAMES
ee0c15a3a0ee  mongo  "docker-entrypoint.s…"  13 seconds ago  Up 11 seconds  0.0.0.0:8589->27017/tcp   demo
```
