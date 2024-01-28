---
author: facsert
pubDatetime: 2023-04-07  15:28:43
title: Linux Package
slug: Linux Package
featured: false
draft: false
tags:
  - Linux
description: "Linux 包管理"
---

<!--
 * @Author       : facsert
 * @Date         : 2023-04-07  15:28:43
 * @LastEditTime : 2023-07-28 12:00:50
 * @Description  : edit description
-->

## Table of Contents

## 包管理工具

### [yum](https://linux.alianga.com/c/yum.html)

ellowdogUpdater,Modified: centos redhat 包管理器

```bash
 $ yum <opt> <pack>                              # RedHat Centos 包管理工具

 $ yum install <pack>                            # 下载安装包
 $ yum update <pack>                             # 更新已安装的包
 $ yum remove <pack>                             # 卸载已安装的包
 $ yum search <pack>                             # 检查软件包的信息

 $ yum list                                      # 列出所有已安装的包
 $ yum list installed <pack>                     # 检查包是否安装

 $ yum list | grep zsh
 > Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
 > zsh.x86_64                                 5.0.2-34.el7_8.2            base
 > zsh-html.x86_64                            5.0.2-34.el7_8.2            base

```

注: yum 配置文件 `/etc/yum.repos.d/`
只下载安装包而不安装
command: `yum reinstall --downloadonly --downloaddir=<path> <package name>`

### [rpm](https://linux.alianga.com/c/rpm.html)

RedHat Package Manager

```bash
 $ rpm <opt> <pack>

 $ rpm -i <pack>                                 # 安装 rpm 包
       -v                                        # 显示安装信息
       -h                                        # 安装包时列出标记
       -e                                        # 卸载 rpm 包

$ rpm -ivh <pack>                                # 安装 rpm 包, 显示安装过程
```

### [apt-get](https://linux.alianga.com/c/apt-get.html)

Advanced Package Tool get: ubuntu debian 的包管理器

```bash
 $ apt <opt> <pack>

 $ apt-get install <pack>                        # 下载并安装软件包
 $ apt-get remove <pack>                         # 卸载已安装的软件包

 $ apt-get upgrade                               # 更新所有已安装的软件包
 $ apt-get update                                # 更新软件包列表

 $ apt install <pack>                            # apt 集成 apt-get 功能
 $ apt download <pack>                           # 仅下载包, 不安装
 $ apt search <pack>                             # 查找 包
 $ apt remove <pack>                             # 卸载已安装的包

 $ apt list --installed | grep zsh               # 列出已安装的包
 > zsh-common/oldstable,oldstable,now 5.7.1-1+deb10u1 all [installed,automatic]
 > zsh/oldstable,oldstable,now 5.7.1-1+deb10u1 amd64 [installed]
```

注: OS 默认下载源文件 `/etc/apt/source.list`
只下载包而不安装, (下载的包会存放在 `/var/cache/apt/archives`)
command: `apt install -d <package name>`

### [dpkg](https://linux.alianga.com/c/dpkg.html)

Debian package: 离线包安装

```bash
 $ dpkg <opt> <pack>

 $ dpkg -i <pack>                                # 安装 deb 安装包
 $ dpkg -r <pack>                                # 卸载 deb 包

 $ dpkg -l                                       # 列出所有安装的包
 > Desired=Unknown/Install/Remove/Purge/Hold
 > | Status=Not/Inst/Conf-files/Unpacked/halF-conf/Half-inst/trig-aWait/Trig-pend
 > |/ Err?=(none)/Reinst-required (Status,Err: uppercase=bad)
 > ||/ Name                     Version                    Architecture Description
 > +++-=========================================-==========================================-============-===========
 > ii  acl                      2.2.53-4                   amd64        access control list - utilities
 > ii  adduser                  3.118                      all          add and remove users and groups
 > ii  adwaita-icon-theme       3.30.1-1                   all          default icon theme of GNOME
 > ii  alien                    8.95                       all          convert and install rpm and other packages
 > ii  apache2                  2.4.38-3+deb10u4           amd64        Apache HTTP Server
```

## 压缩 解压

### [tar](https://linux.alianga.com/c/tar.html)

```bash
 $ tar <opt> <pack>

 $ tar -zxvf <package name> -C <path>             # 解压 tar.gz 压缩包到 <path> 路径
 > tar -zxvf package.tar.gz -C /root/             # 将 package.tar.gz 解压到 /root/ 目录下

 $ tar -zcvf <package name> <file|dir>           # 将文件或目录压缩
 > tar -zcvf learn.tar.gz learn/                 # 将当前目录下的 learn 文件夹压缩成 learn.tar.gz


 $ tar -ztvf <tar.gz>                            # 列出压缩包的文件
 > drwxr-xr-x root/root         0 2023-04-03 03:24 test/
 > -rw-r--r-- root/root         0 2023-04-03 03:24 test/3rd
 > -rw-r--r-- root/root         0 2023-04-03 03:24 test/2nd
 > -rw-r--r-- root/root         0 2023-04-03 03:24 test/1st
```

|            parameter             | meaning                     |
| :------------------------------: | :-------------------------- |
|          `-c, --create`          | 创建一个新归档              |
|       `-f, --file=ARCHIVE`       | 使用归档文件或 ARCHIVE 设备 |
|           `-t, --list`           | 列出归档内容                |
|         `-v, --verbose`          | 详细地列出处理的文件        |
|      `-x, --extract, --get`      | 从归档中解出文件            |
| `-z, --gzip, --gunzip, --ungzip` | 通过 gzip 过滤归档          |

### [zip](https://linux.alianga.com/c/zip.html)

```bash
 $ zip <opt> <pack>

 $ zip <package name> <file>                     # 压缩文件成 zip 包
 $ zip -r <package name> <path>                  # 压缩文件夹成 zip 包
 > zip -r learn.zip learn/                       # 将当前目录下的 learn 文件夹压缩成 learn.zip

 $ unzip <package name>                          # 解压 zip 压缩包
 $ unzip -v <zip>                                # 查看 zip 压缩文件内容
 > Archive:  test.zip
 >  Length   Method    Size  Cmpr    Date    Time   CRC-32   Name
 > --------  ------  ------- ---- ---------- ----- --------  ----
 >       0  Stored        0   0% 2023-04-03 03:24 00000000  test/
 >       0  Stored        0   0% 2023-04-03 03:24 00000000  test/3rd
 >       0  Stored        0   0% 2023-04-03 03:24 00000000  test/2nd
 >       0  Stored        0   0% 2023-04-03 03:24 00000000  test/1st
 > --------          -------  ---                            -------
 >      0                0   0%                            4 files
```
