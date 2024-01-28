---
author: facsert
pubDatetime: 2022-07-19 15:28:43
title: Linux Basic
slug: Linux Basic
featured: false
draft: false
tags:
  - Linux
description: "Linux 基础使用"
---

<!--
 * @Author       : facsert
 * @Date         : 2022-07-19 15:28:43
 * @LastEditTime : 2023-07-29 16:29:55
 * @Description  : edit description
-->

## Table of Contents

[linux 命令](https://www.linuxcool.com/)

## 帮助信息

```bash
 $ man <command>                                 # man (manual) 帮助手册, 查看命令的帮助手册
 $ man -f <command>                              # 显示命令的简短描述
 $ man -f date
 > date (1)             - print or set the system date and time

 $ whatis <command>                              # 显示命令的简短描述, 等同于 man -f <command>
 $ whatis date
 > date (1)             - print or set the system date and time

 $ <command> -h/--help                           # 命令使用 -h/--help 参数
 $ date -h
 $ date --help
 > Usage: date [OPTION]... [+FORMAT]
 >   or:  date [-u|--utc|--universal] [MMDDhhmm[[CC]YY][.ss]]
 > Display the current time in the given FORMAT, or set the system date.
```

## 文件与路径

```bash
 $ pwd                                           # 查看当前目录
 > /root/Desktop

 $ ls                                            # 查看当前目录下的文件及文件夹
 > source.sh  target.tar.gz

 $ cd <path>                                     # 切换到 path 目录(无返回值), path 不存在则报错
 ~/Desktop/Zip$                                  # 命令行 $ 左边即为当前目录
```

```bash
 $ cp <source> <destination>                     # 文件复制
 $ mv <source> <destination>                     # 文件移动或重命名
 $ rm <file>                                     # 删除文件
 $ mkdir <path>                                  # 创建文件夹
 $ touch <file>                                  # 创建文件
 $ cat <file>                                    # 查看文件内容

 $ touch learn.log                               # 创建文件 learn.log
 $ mkdir seeds                                   # 创建文件夹 seeds
 $ cp learn.log seeds/                           # learn.log 复制到 seeds 文件夹下
 $ rm learn.log                                  # 删除当前目录下的 learn.log
 $ mv seeds/learn.log study.log                  # 将 seeds 下的 learn.log 移动到当前目录并重命名
 $ rm -r seeds                                   # 删除 seeds 文件夹及文件夹下的所有文件

 > 'learn.log'       -> create
 > 'seed/'           -> create
 > 'learn.log'       -> 'seed/learn.log'
 > 'learn.log'       -> delete
 > 'seeds/learn.log' -> 'study.log'
 > 'seed/'           -> delete
```

## 查看执行过命令

```bash
 $ history                                       # 查看执行的命令历史
 > 1 ls
 > 2 date

 $ !!                                            # 执行上一条命令
 $ !<command key> + enter                        # 通过命令关键字和 enter 执行上一个包含关键字的命令

 $ control + r + <command key>                   # 快捷键进入历史列表, 通过关键字查找, enter 执行命令
 > (reverse-i-search)`echo $': echo $PWD         # `echo $' 是输入关键字, : 后为匹配的命令, 方向键选择命令但不执行
```

## 全局变量

全局变量是在整个操作系统中都可用的变量，其值可以在任何地方被访问和修改

```bash
 $ echo $HOME                                    # 当前用户名
 > root

 $ echo $PATH                                    # 执行命令时所搜寻的路径列表, 以 : 分隔(环境变量设置路径)
 > /usr/local/sbin:/usr/local/bin:/usr/bin

 $ echo $SHELL                                   # 当前使用的 shell
 > /bin/zsh

 $ echo $PWD                                     # 当前绝对路径
 > /root/Desktop/

 $ echo $RANDOM                                  # 生成随机数字
 > 2166
```

修改 shell 启动文件(每次打开命令行均会执行)以永久设置环境变量, 使用 export 添加环境变量

```bash
export <variable name>=<value>                   # 使用 export 临时修改环境变量, 执行的命令窗口关闭即失效
export PATH=$PATH:/home/facsert                  # 将 /home/facsert 临时加入环境目录
export LEARN=TRUE                                # 自定义新的环境变量, 脚本间可以通过自定义环境变量通信
export PATH=$PATH:/home/facsert                  # 将命令写入 shell 启动文件(每次启动 shell 均会执行文件上命令)
source ~/.bashrc                                 # 重新加载 shell 启动文件(~/.bashrc, ~/.zshrc )
```

bash 执行脚本: 新建子 shell 执行脚本, 脚本中的 export 的变量只在子 shell 生效, 脚本结束变量失效
source 执行脚本: 脚本中的 export 变量加载入当前终端, 脚本结束仍然生效, 当前终端关闭后失效

```bash
 $ echo 'export linux="bash"' > bash.sh          # 生成脚本 bash.sh
 $ echo 'export linux="source"' > source.sh      # 生成脚本 source.sh

 $ bash bash.sh && echo "linux: $linux"          # 使用 bash 添加 linux 变量
 > linux:                                        # 脚本结束, 变量失效

 $ source source.sh && echo "linux: $linux"      # 使用 source 执行脚本
 > linux: source shell                           # 脚本结束, 变量仍然生效
```

## 系统相关

```bash
 $ shutdown -h now                               # 关机
 $ shutdown -r now                               # 重启

 $ date                                          # 系统时间
 > Sat Feb 25 20:34:10 CST 2023

 $ unmae -a                                      # 查看系统内核信息
 > inux facser 5.15.79.1-microsoft-standard-WSL2 #1 SMP Wed Nov 23 01:01:46 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux

 $ ps -ef                                        # 查看当前正在运行的所有进程
 > UID        PID  PPID  C STIME TTY          TIME CMD
 > root      1399    71  0 19:08 pts/4    00:00:00 /bin/zsh -i
 > root      9481   490  0 20:24 pts/4    00:00:00 bash -x a.sh

 $ kill <pid>                                    # 根据 pid 关掉指定进程
 $ kill 9481
 > [1]  + 9481 terminated  bash -x a.sh
```

## 用户

### 启用 root 用户

```bash
 $ sudo passwd root
 > Enter new UNIX password:                      # 设置 root 用户密码(输入时密码不可见)
 > Retype new UNIX password:                     # 重复输入 root 密码
 > passwd: password updated successfully         # 设置成功

 facser@master:~/Desktop$ su root                # 切换 root 用户 (用户名@hostname:当前路径)
 root@master:~/Desktop# exit                     # 退出 root, 普通用户 $ 开头, root 用户 # 开头

 $ su <user>                                     # 切换用户
```

### 创建用户

```bash
 $ useradd -m <username>                         # 创建用户, 并在 /home 生成用户目录
 $ useradd -l <oldName> <newName>                # 更换用户名称

 $ passwd <username>                             # 用户添加密码
 > New password:                                 # 输入密码, 密码不显示
 > Retype new password:                          # 重复输入密码, 密码不显示

 $ userdel -r <username>                         # 删除用户
 > no crontab for facsert
```

sudo 免密码

```bash
 $ sudo vi /etc/sudoers                          # 编辑配置文件

 > %sudo ALL=(ALL:ALL) ALL                       # 找到这一栏, 建议注释掉复制一行修改
 > %sudo ALL=(ALL:ALL) NOPASSWD:ALL              # 修改后, 强制保存退出
 > <username> ALL=(ALL) NOPASSWD:ALL
```

注: 该文件必须强制写入, 不能修改文件权限, 否则报错

## 通配符号

### `？` 任意单个字符

```bash
 $ touch ab a bc abc                             # 生成 ab a ba abc 4 个文件

 $ ls ?b                                         # ? 可以指代任意单个字符
 > ab

 $ ls b?
 > bc
```

### `*` 任意多个字符

```bash
 $ touch ab a bc abc                             # 生成 ab a ba abc 4 个文件

 $ ls *                                          # * 可以指代任意多个字符
 > a  ab  abc  bc

 $ ls a*
 > a  ab  abc
```

### `**` 任意多级目录

```bash
 $ cat /etc/**/word                               # /etc/ 文件夹下所有 word 文件

 $ ls learn/**/*.md                               # 显示 learn 目录下所有 markdown 文件
 > a.md b.md c.md
```

### `[]` 匹配范围

```bash
 $ touch aa ab ac aab                            # 生成 ab a ba abc 4 个文件

 $ ls a[ab]                                      # 匹配 [] 内任意一个字符
 > aa ab
```

## 特殊符号

### 输出重定向

| 输出重定向 | 输出重定向追加写入 | 输入重定向 | 读取标准输入直至分界符号 |
| :--------: | :----------------: | :--------: | :----------------------: |
|    `>`     |        `>>`        |    `<`     |           `<<`           |

```bash
 $ <command> > <file>                            # 将命令返回值覆盖写入文件(原文件清空后写入)
 $ date > file.log                               # 将 date 命令返回值写入 file.log 文件, 命令行不显示结果
 > Sat Feb 25 20:39:56 CST 2023

 $ <command> >> <file>                           # 将输出追加写入文件(原文件内容保留)
 $ date >> file.log                              # 将 date 命令返回值追加写入 file.log 内容结尾, 命令行不显示结果
 > Sat Feb 25 20:39:56 CST 2023
 > Sat Feb 25 20:40:51 CST 2023

 $ <command> < <file>                            # 将文件内容重定向为标准输入
 $ cat < file.log                                # 读取 file.log 并打印
 > Sat Feb 25 20:39:56 CST 2023
 > Sat Feb 25 20:40:51 CST 2023

 $ touch $(< fileName.log)                       # 根据文件内的文件列表生成文件

 $ cat << EOF                                    # 实现多行输入, 分界符号 EOF 可自定义
 > line
 > line
 > EOF
```

`&1`: 标准输出, 命令行输出, 能直接重定向至文件  
`&2`: 标准错误, 命令执行错误输出, 需要将错误重定向至标准输出再指向文件  
`/dev/null`: 空, 指代垃圾桶或回收站

```bash
 $ mian                                          # 错误命令,显示错误输出
 > Command 'mian' not found

 $ data 2>/dev/null                              # 错误命令, 错误输出重定向至回收站, 不显示错误
 $ date 2>/dev/null                              # 正确命令, 标准输出重定向至回收站, 不显示返回值

 $ data 2 > date.log                             # 命令行显示错误, 文件不显示, 错误输出无法直接重定向文件
 $ data > date.log 2>&1                          # 命令行不显示错误, 文件内显示, 错误输出重定向至文件
```

## 单词缩写

| 缩写  | 全称                | 翻译        |
| :---: | :------------------ | :---------- |
|   a   | `all`               | 全部        |
|   b   | `backup`            | 备份        |
|   c   | `count`             | 计数        |
|   d   | `directory`         | 文件        |
|   f   | `force`             | 强制        |
|   h   | `help`              | 帮助        |
|   i   | `interactive`       | 交互的      |
|   l   | `long`              | 长格式的    |
| r / R | `recursive`         | 递归        |
| s / S | `sort/size`         | 排序/大小   |
|   t   | `time`              | 时间        |
|   u   | `user`              | 用户        |
|   v   | `version / verbose` | 版本/详细的 |

### 符号

| 符号 | 含义                   |
| :--: | :--------------------- |
| `>`  | 输出重定向, 覆盖       |
| `>>` | 输出重定向, 追加       |
| `&`  | 程序放入后台执行       |
| `#`  | 注释                   |
| `&&` | 且, 左边成功才执行右边 |

### 正则表达式

|   符号   | 模式                          |
| :------: | :---------------------------- |
|   `^`    | 锚定行首                      |
|   `$`    | 锚定行尾                      |
|   `()`   | 捆绑成一个整体                |
|   `.`    | 任意一个字符                  |
|   `?`    | 前面字符或模式 0 或 1 次      |
|  `{m}`   | 前面字符或模式 m 次           |
| `{m, n}` | 前面字符或模式次数在 m n 之间 |
|   `+`    | 前面字符或模式 1 次或多次     |
|   `*`    | 前面字符或模式任意次          |
|   `[]`   | 括号范围内均可                |
|  `[^]`   | 括号范围之外均可              |
