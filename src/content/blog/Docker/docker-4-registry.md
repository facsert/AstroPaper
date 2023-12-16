---
author: facsert
pubDatetime: 2023-11-01 09:15:02
title: Docker Registry
postSlug: ""
featured: false
draft: false
tags:
  - docker
description: "Docker 私有仓库"
---

<!--
 * @Author       : facsert
 * @Date         : 2023-11-01 09:15:02
 * @LastEditTime : 2023-11-06 09:40:32
 * @Description  : edit description
-->

创建本地 docker 仓库

```bash
 $ docker pull registry

 $ docker images
 > registry  latest  3c11102b9417   2 months ago    23.7MB

 $ docker run -d -p 5000:5000 \                  # 宿主 5000 端口映射到容器 5000 端口
   --restart=always \                            # 容器随宿主启动
   --volume /opt/registry:/var/lib/registry \    # 宿主目录映射到容器目录
   --name registry registry                      # 容器名称 镜像名称

 $ docker ps
 > 9f5e1fa8dab6  registry:latest  "/entrypoint.sh /etc…"   8 weeks ago   Up 3 weeks   0.0.0.0:5000->5000/tcp, :::5000->5000/tcp
```

注: `/var/lib/registry` 是容器内存放镜像的路径

## 本地配置

本地配置 docker

```bash
 $ ifconfig
 > ......
 > 10.58.14.96                                   # 查看宿主机器 IP
 > ......

 $ vim /etc/docker/daemon.json
 > ......
 > "insecure-registries": [
        "10.58.14.96:5000"                        # 添加本地仓库地址
   ]
 > ......

 $ systemctl daemon-reload                                                     # 重新加载 docker 配置文件
 $ systemctl restart docker                                                    # 重新启动 docker
```

## 推送镜像

将本地镜像推送至本地仓库

```bash
 $ docker images
 > REPOSITORY               TAG       IMAGE ID       CREATED         SIZE
 > mysql                    latest    bdfb0ec4d54a   2 months ago    599MB

 $ docker tag mysql:latest 10.58.14.96:5000/mysql:latest
 $ docker images
 > REPOSITORY               TAG       IMAGE ID       CREATED         SIZE
 > mysql                    latest    bdfb0ec4d54a   2 months ago    599MB
 > 10.58.14.96:5000/mysql   latest    bdfb0ec4d54a   2 months ago    599MB

 $ docker push 10.58.14.96:5000/mysql:latest
 $ curl -XGET  http://10.58.14.96:5000/v2/_catalog
 > {"repositories":["mysql"]}
```

## 镜像管理

管理私有仓库镜像

```bash
 $ curl -XGET  http://10.58.14.96:5000/v2/_catalog
 > {"repositories":["mysql"]}

 $ curl -XGET  http://10.58.14.96:5000/v2/mysql/tags/list
 > {"name":"mysql","tags":["latest"]}
```

删除私有仓库镜像, 官方删除方式只删除元数据, 镜像未删除  
可以直接删除镜像文件方式删除仓库镜像

```bash
  # 直接进入映射路径删除镜像文件或进入容器路径删除镜像
  $ rm -rf {map_path}/docker/registry/v2/repositories/{image}
  $ docker exec registry rm -rf /var/lib/registry/docker/registry/v2/repositories/{images}

  $ rm -rf /var/lib/registry/docker/registry/v2/repositories/mysql

 $ curl -XGET  http://10.58.14.96:5000/v2/_catalog
 > {"repositories":[]}
```
