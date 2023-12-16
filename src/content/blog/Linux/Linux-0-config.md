---
author: facsert
pubDatetime: 2023-11-01 21:22:09
title: Linux Configuration
postSlug: ""
featured: false
draft: false
tags:
  - Linux
description: "Linux 配置"
---

<!--
 * @Author       : facsert
 * @LastEditTime : 2023-12-10 22:05:34
 * @LastEditTime : 2023-11-01 21:22:09
 * @Description  : edit description
-->

Linux 配置

## 网络配置

Debian 网络配置

```bash
 $ vi /etc/network/interfaces

 auto enp125s0f0                                 # 网卡名
 iface enp125s0f0 inet static                    # 设置静态 IP
 address 10.58.14.96/24                          # 设置 IP 地址和掩码
 gateway 10.10.10.1                              # 设置网关

 auto enp125s0f0                                 # 网卡名称
 iface enp125s0f0 inet dhcp                      # 设置动态 IP
 address 10.58.14.96/24                          # 设置 IP 地址和子网掩码
 gateway 10.10.10.1                              # 设置网关

 $ systemctl restart networking                  # 重启网络服务
```

Centos

```bash
 vi /etc/sysconfig/network-scripts/ifcfg-eth0

 TYPE=Ethernet                                   # 类型=以太网络
 PROXY_METHOD=none                               # 代理模式
 BROWSER_ONLY=no
 DEFROUTE=yes
 IPV4_FAILURE_FATAL=no
 IPV6INIT=yes                                    # 启用IPV6协议
 IPV6_AUTOCONF=yes                               # 自动配置IPV6地址
 IPV6_DEFROUTE=yes
 IPV6_FAILURE_FATAL=no
 IPV6_ADDR_GEN_MODE=stable-privacy
 NAME=ens32
 UUID=0e6d72a7-8a6c-43ac-aef2-25d165562fd0　　

 DEVICE=eth0                                     # 设备名称
 BOOTPROTO=none                                  # 开机协议 static dhcp none
 ONBOOT=yes                                      # 启动或重启网络时，启动该设备，yes启动，no不启动
 IPADDR=192.168.1.15                             # IPV4 地址
 PREFIX=24                                       # 子网掩码
 GATEWAY=192.168.1.1                             # 网关
 DNS1=192.168.1.2                                # DNS服务器地址
 IPV6_PRIVACY=no                                 # IPV6协议

 $ systemctl restart network                     # 重启网络
```

## vim

Debian vim 初始设置难以使用, 修改 vim 配置文件

```bash
 $ vi /etc/vim/vimrc.tiny

 set nocompatible                                # 去掉 vi 兼容模式
 set backspace=2                                 # 设置退格键为删除键
 set mouse=a                                     # 设置鼠标为支持
 set ruler                                       # 打开状态栏标尺
 set number                                      # 显示行号
```

## 配置 ssh

```bash
 $ vi /etc/ssh/sshd_config                       # 编辑 ssh 系统配置文件

 #PermitRootLogin prohibit-password
 PermitRootLogin yes                             # 允许远程 root 用户登录

 #PasswordAuthentication no
 PasswordAuthentication yes                      # 允许用户密码作为口令验证

 $ systemctl restart sshd                        # 重启 ssh 服务，令配置生效
```

## host 配置

命令行开头 `root@debian:~/#`

- root: 用户名称
- debian: Linux host 名称
- ~/: 当前目录
- #: root 用户标识(普通用户为 $)

```bash
 $ hostname                                      # 打印本机 hostname
 > debian

 $ hostname -i                                   # 打印本机 IP
 > debian

 $ hostnamectl set-hostname ubuntu               # 修改 hostname, 立即生效
 $ hostname
 > ubuntu
```

通过 `/etc/hosts` 文件可以将主机名与 IP 绑定快速解析

```bash
 $ vi /etc/hosts                                 # 域名解析为 IP 地址的配置文件

 127.0.0.1        localhost                      # localhost 会被解析为 IP 127.0.0.1
 192.168.1.49     node                           # node 被解析为对应 IP, 例如 ping node == ping 192.168.1.49
 123.123.123.123  baidu
```

通过 `/etc/resolve.conf` 指定 DNS 解析的服务器地址
用户通过域名访问网站, 请求发送到 DNS 服务器, DNS 服务器将域名解析为具体的 IP 返回给用户机器

```bash
 $ vi /etc/resolve.conf

 nameserver  8.8.8.8                             # 添加 DNS 解析服务器 IP 地址
 nameserver  10.10.10                            # 添加 DNS 解析服务器 IP 地址

 $ source /etc/resolve.conf                      # 配置生效
```

```bash
      www.baidu.com     123.123.123.123    123.123.123.123
computer -------> DNS server -------> computer -------> baidu server

```

## Windows WSL ssh 连接

WSL 下查看 ssh 服务端口

```bash
 $ ss -ntlp | grep ssh                           # Debian Ubuntu 默认端口查看工具
 > LISTEN   0   128   0.0.0.0:2222   0.0.0.0:*  users:(("sshd",pid=4628fd=4))
 > LISTEN   0   128      [::]:2222      [::]:*  users:(("sshd",pid=4628,fd=3))

 $ netstat -ntlp | grep ssh                      # Redhat Centos netstat 查看端口
 > tcp    0   0 0.0.0.0:2222   0.0.0.0:*   LISTEN   4628/sshd: /usr/sbi
 > tcp6   0   0 :::2222        :::*        LISTEN   4628/sshd: /usr/sbi

 $ systemclt start sshd                          # 启动 ssh 服务
 $ service ssh start
 $ /etc/init.d ssh start

 $ systemctl ssh enable                          # 设置 ssh 自启动
 $ update-rc.d ssh enable
```

Windows > 设置 > 应用 > 可选功能 > 添加可选功能  
选择 OpenSSH 服务端和 OpenSSH 服务端安装  
安装后在可选功能界面下方检查是否安装成功

Windows 打开终端管理员, 设置 WSL IP 和 ssh 端口映射到 Windows 端口

```powershell
# 添加 Windows 端口映射
netsh interface portproxy add v4tov4 listenport=2222 listenaddress=0.0.0.0 connectport=2222 connectaddress=172.28.185.15

# listenport windows 端口
# listenaddress windows IP
# connectport WSL 端口
# connectaddress WSL IP

# 查看端口映射列表
netsh interface portproxy show all
侦听 ipv4:                 连接到 ipv4:

地址            端口        地址            端口
--------------- ----------  --------------- ----------
0.0.0.0         2222        172.28.185.15   2222

# 删除端口映射
netsh interface portproxy delete v4tov4 listenport=2222 listenaddress=0.0.0.0
```

## WSL 启用 systemctl

修改 `/etc/wsl.conf` 文件, 重启 wsl

```bash
 $ vi /etc/wsl.conf
 [boot]
 systemd=true
```

```powershell
 wsl --shutdown
 wsl
```

## 自定义服务

自定义系统或者用户服务, 通过 service 或 systemctl 命令管理

### 注册服务

|   systrmctl 脚本    |          系统服务          |          用户服务          |
| :-----------------: | :------------------------: | :------------------------: |
| `/usr/lib/systemd/` | `/usr/lib/systemd/system/` | `/usr/lib/systemd/system/` |

在 `/usr/lib/systemd/system/` 路径下创建 service 文件  
`vi /usr/lib/systemd/system/script.service` 创建 script 服务(**文件名即服务名**)

```ini
[Unit]
Description=Personal service

[Service]
Type=forking
ExecStart=/root/script.sh -start
ExecStop=/root/script.sh -stop

[Install]
WantedBy=multi-user.target
```

### 字段解析

```bash
[Unit]
Description: 服务描述
After: 在什么服务之后启动 ex:network.target, sshd-keygen.service
Before: 在什么服务之前启动 ex:local-fs.target, sshd.service

[Service]
Type: 服务类型
  simple: 默认值, 前台执行, ExecStart启动的进程为主进程
  forking: 后台执行, ExecStart父进程将会退出, 子进程将成为主进程
  oneshot: 只执行一次, Systemd 会等它执行完, 才启动其他服务
  idle: 等到其他任务都执行完, 才会启动该服务
ExecStart: 启动服务时执行的命令
ExecStop: 停止服务时执行的命令
ExecReload: 重启服务时执行的命令
ExecStartPre: 启动服务之前执行的命令
ExecStartPost: 启动服务之后执行的命令
ExecStopPost: 停止服务之后执行的命令
Restart: 重启方式
  no: 默认值, 退出后不重启
  always: 总是重启
  on-success: 只有正常退出时才重启
  on-failure: 状态码非0才重启
  on-abnormal: 被信号终止和超时时才重启
  on-watchdog: 超时退出时才重启
RestartSec: 重启间隔
TimeoutSec: systemd 停止服务超时时间

[Install]
WantedBy: 服务所在 target, 多用户模式下需要, multi-user.target
Alias: 服务别名
```

### 服务管理

```bash
systemctl daemon-reload                          # 重载服务系统
systemctl enable script.service                  # 设置开机启动
systemctl disable script.service                 # 禁用开机启动
systemctl start script.service                   # 启动服务
systemctl status script.service                  # 查看服务状态
systemctl stop script.service                    # 停止服务
systemctl reload script.service                  # 重新加载服务
```
