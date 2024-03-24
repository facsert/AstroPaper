---
author: facsert
pubDatetime: 2022-10-10 15:28:43
title: Docker image
slug: Docker image
featured: false
draft: false
tags:
  - docker
description: "Docker 镜像"
---

<!--
 * @Author       : facsert
 * @Date         : 2022-10-10 15:28:43
 * @LastEditTime : 2023-07-28 17:01:11
 * @Description  : edit description
-->

## Table of Contents

## 介绍

镜像是容器的模板, 所有容器都通过镜像创建的  
一个镜像可以生成任意数量的容器  
可以使用官方镜像或者自定义镜像

## 镜像列表

```bash
 $ docker images [OPTIONS] [REPOSITORY[:TAG]]
 Options:
  -a, --all             Show all images (default hides intermediate images)
      --digests         Show digests
  -f, --filter filter   Filter output based on conditions provided
      --format string   Pretty-print images using a Go template
      --no-trunc        Do not truncate output
  -q, --quiet           Only show numeric IDs

 $ docker images
 > REPOSITORY  TAG     IMAGE ID      CREATED       SIZE
 > mongo       latest  0850fead9327  12 days ago   700MB
 > pipreqs     1.0     d4633ae3a8af  2 months ago  1.08GB
```

| `REPOSITORY` | `TAG`    | `IMAGE ID` | `CREATED`    | `SIZE`   |
| :----------- | :------- | :--------- | :----------- | :------- |
| 镜像仓库源   | 镜像 tag | 镜像 ID    | 镜像创建时间 | 镜像大小 |

## 拉取镜像

- 官方镜像
- 私有镜像

拉取镜像前可以使用 docker search 查看官方及第三方镜像列表  
未指定链接默认从官方镜像拉取

```bash
 $ docker pull [OPTIONS] NAME[:TAG|@DIGEST]
 Options:
   -a, --all-tags                Download all tagged images in the repository
       --disable-content-trust   Skip image verification (default true)

 $ docker pull ubuntu:20.04

 # 下载指定平台的镜像
 $ docker pull --platfrom linux/amd64 mysql
 $ docker pull --platfrom linux/arm64 mysql
```

从私有仓库拉取镜像前需要先登录, 然后指定仓库中的镜像拉取  
在私有仓库中未找到镜像会到官方镜像仓库拉取

```bash
 $ docker login [person registry]
 > docker login registry.facser:8081
 Username (facser): facser
 Password:
 Login Succeeded

 $ docker pull registry.facser:8081/ubuntu:20.04
```

## 删除镜像

```bash
 $ docker rmi [OPTIONS] IMAGE [IMAGE...]
 Options:
   -f, --force      Force removal of the image
       --no-prune   Do not delete untagged parents

 $ docker images
 > REPOSITORY  TAG     IMAGE ID      CREATED       SIZE
 > mongo       latest  0850fead9327  12 days ago   700MB
 > pipreqs     1.0     d4633ae3a8af  2 months ago  1.08GB

 $ docker rmi 0850fead9327
```

## 其它操作

- 查找镜像

```bash
 $ docker search [OPTIONS]  [IMAGE NAME]

 $ docker search ubuntu
 > NAME               DESCRIPTION                                     STARS   OFFICIAL   AUTOMATED
 > ubuntu             Ubuntu is a Debian-based Linux operating sys…   15368   [OK]
 > websphere-liberty  WebSphere Liberty multi-architecture images …   290     [OK]
```

| `NAME`   | `DESCRIPTION` | `STARS` | `OFFICIAL` | `AUTOMATED`  |
| :------- | :------------ | :------ | :--------- | :----------- |
| 镜像名称 | 镜像名称      | 点赞数  | 是否官方   | 是否自动构建 |

- 保存镜像

```bash
 $ docker save [OPTIONS] IMAGE [IMAGE...]
 $ docker save 0850fead9327 > mongo.tar.gz
 $ docker save -o mysql.tar mysql:lates

 $ ll
 > -rw-r--r-- 1 root  root   23K Mar  7 21:10 mongo.tar.gz

 # 导入镜像
 $ docker load -i mysql.tar
```

- 镜像历史

```bash
 $ docker history [OPTIONS] IMAGE
 $ docker history 0850fead9327

 > IMAGE         CREATED      CREATED BY                          SIZE  COMMENT
 > 0850fead9327  12 days ago  /bin/sh -c #(nop)  CMD ["mongod"]   0B
```

- 修改镜像 tag

```bash
 $  docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]

❯ docker images
REPOSITORY  TAG  IMAGE ID      CREATED       SIZE
mongo-base  v1   43761bd5b76d  41 hours ago  00MB
mongo-base  v2   43761bd5b76d  41 hours ago  700MB

$ docker tag mongo-base:v2  mongo-person:1.0.0

❯ docker images
REPOSITORY    TAG    IMAGE ID      CREATED       SIZE
mongo-base    v1     43761bd5b76d  41 hours ago  700MB
mongo-person  1.0.0  43761bd5b76d  41 hours ago  700MB
```

## 自定义镜像

### docker build

```bash
 docker build [OPTIONS] PATH | URL | -

 Options:
   --file, -f                                                                  # 指定要使用的Dockerfile路径；
   --tag, -t                                                                   # 镜像的名字及标签(name:tag 或 name, 允许多个)
   --no-cache                                                                  # 不使用缓存

 $ docker build -t nginx-base:v1 .                                             # "." 查找当前目录下的 Dockerfile 构建镜像
 $ docker build -t nginx-base:v1 -f /root/Dockerfile                           # 指定 Dockerfile 构建镜像

REPOSITORY  TAG  IMAGE ID      CREATED       SIZE
nginx-base  v1   43761bd5b76d  41 hours ago  700MB
```

### Dockerfile

```bash
FROM ubuntu                                                                    # 以 ubuntu 镜像为基础, 可添加 tag, ubuntu:20.04

ENV path=/usr/local/                                                           # 设置全局变量, 可添加多个，或 ENV 多次设置, 可以使用已设置的变量
ARG USERNAME="facser"                                                          # 设置 build 执行参数, 通过 --build-arg <key>=value 来修改

LABEL version="1.0.0" description="ubuntu image by $USERNAME"                  # 添加镜像元数据

ADD nginx-1.8.0.tar.gz $path                                                   # 将系统下文件复制到镜像中目录下
ADD epel-release-latest-7.noarch.rpm $path

WORKDIR $path                                                                  # 设定镜像中工作目录, 并转到改目录, 类似 cd 命令, 可以多次设置

RUN rpm -ivh /usr/local/epel-release-latest-7.noarch.rpm &&\                   # 执行 shell 命令
    yum install -y wget lftp gcc gcc-c++ make openssl-devel pcre-devel pcre &&\
    yum clean all &&\
    cd $path/nginx-1.8.0 &&\
    ./configure --prefix=/usr/local/nginx --user=www --group=www --with-http_ssl_module --with-pcre &&\
    make &&\
    make install &&\
    echo "daemon off;" >> /etc/nginx.conf

CMD /usr/sbin/nginx                                                            # 生成容器后执行的命令, 会被 docker run 生成容器时初始命令覆盖
```

```bash
FROM IMAGE[:TAG]                                                               # 第一行必须是 FROM, 选择基础镜像, 可以设置多个

FROM ubuntu:20.04
FROM centos
```

```bash
ARG <name>[=<default value>]                                                   # 设置构建镜像的外部参数, 修改参数需要使用 --build-arg
ENV <key> <value>                                                              # 设置 Dockerfile 内全局变量

ARG USERNAME="facser"                                                          # Dockerfile 设置默认参数 "facser"
$ docker build --build-arg USERNAME="kertory" -t demo:v1                       # build 镜像时通过 --build-arg 修改 USERNAME 的值

ENV FILENAME "record.log"                                                      # 设置 Dockerfile 内全局变量, 一般全大写, 用法与 shell 一致
ENV TITLE="title in $FILENAME"                                                 # "title in record.log", 使用 = 可以设置多个变量
```

```bash
COPY <src> <dest>
ADD  <src> <dest>

COPY client.log /root/docker.log                                               # 将容器外的文件复制到镜像指定目录下
ADD  /root/client.log /root/docker.log                                         # 使用绝对路径, 效果一致
```

```bash
WORKDIR <path>                                                                 # 设置镜像内的工作目录, 类似 cd 效果

WORKDIR /root                                                                  # 进入 /root 并设为工作目录
WORKDIR /home                                                                  # 将工作目录转到 /home
WORKDIR facser                                                                 # 将工作目录转到 /home/facser
WORKDIR Desktop                                                                # 将工作目录转到 /home/facser/Desktop
```

```bash
RUN <command>                                                                  # 通过 sh 执行命令
RUN ["executable", "param1", "param2"]                                         # 指定 shell 执行命令

RUN date                                                                       # 通过 sh 执行 date 命令 (命令执行失败即停止)
RUN ["/bin/sh", "-c", "date"]                                                  # 和上命令完全一致
RUN ["/bin/bash", "-c", "date"]                                                # 通过 bash 执行 date 命令 (命令执行失败继续执行)

RUN echo "line first"  >> /root/run.log                                        # 执行 3 个 RUN 指令, 创建 3 个镜像层
RUN echo "line second" >> /root/run.log                                        # 层数越多占用空间更大
RUN echo "line third"  >> /root/run.log                                        # 创建失败时, 从失败的上一层 RUN 继续, 重新创建速度更快

RUN echo "line first"  >> /root/run.log &&\                                    # 一个 RUN 指令创建 1 个镜像层
    echo "line second" >> /root/run.log &&\                                    # 只有一层镜像, 占用更小
    echo "line third"  >> /root/run.log                                        # 每次创建都需要从头开始执行
```

```bash
CMD <command>                                                                  # 以次文件构建的镜像, 创建容器后, 通过 sh 执行命令
CMD ["executable", "param1", "param2"]                                         # 以次文件构建的镜像, 创建容器后, 指定 shell 执行命令

CMD /bin/bash                                                                  # 会被 docker run 创建容器时指定的命令覆盖

 $ docker run -it --name nginx-demo nginx-base echo "create mongo container"   # echo 命令覆盖 /bin/bash 指令
```

注: 创建容器时, 若执行的命令在一定时间内能完成, 则容器在执行完命令就会关闭

- echo "hello docker", 创建容器在执行完 echo 命令后便关闭改容器
- /bin/bash, 该命令未收到 exit 退出前会一直执行, 该容器会保持运行状态
