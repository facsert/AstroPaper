---
author: facsert
pubDatetime: 2022-07-03 15:44:44
modDatetime:
title: 01.BASH Basic
slug: 01.BASH Basic
featured: false
draft: false
tags:
  - bash
description: "Bash 基本语法"
---

<!--
 * @Author       : facsert
 * @Date         : 2022-08-09 21:20:45
 * @LastEditTime : 2023-07-28 11:57:23
 * @Description  : edit description
-->

[BASH 教程](https://wangdoc.com/bash/)

## Table of Contents

## 介绍

Bash 是一种 Linux 和 macOS 上的命令行 Shell，它是 Linux 系统中使用最广泛的 Shell  
Shell 是一个提供与用户对话的环境的程序; 又称为命令行环境 command line interface, 简写为 CLI

```bash
 $ cat /etc/shells                               # 查看系统下所有的 shell
 > /bin/bash
 > /bin/csh
 > /bin/dash
 > /bin/ksh
 > /bin/sh
 > /bin/zsh

 $ echo $SHELL                                   # 当前使用的 shell
 > /bin/zsh

 $ chsh -s /bin/bash                             # 修改当前 shell 为 bash
 $ usermod --shell /bin/zsh root                 # 设置 root 用户默认 shell 为 zsh
 $ cat /etc/passwd |grep root                    # 查看 root 用户的默认 shell
```

## 语法

BASH 基础语法

```bash
 $ command <option> <args>                       # 命令 选项 参数, shell 命令一般格式

 $ cat -n log.txt                                # 读取 log.txt 文件, 命令 cat, 选项 -n, 参数 log.txt
 > 1 1st
 > 2 2nd
 > 3 3rd

 $ command | <command> | <command>               # 命令管道
 $ cat log.txt | grep "3rd"                      # 管道前的值作为管道后命令的参数
 > 3rd

 $ grep "3rd" log.txt                            # 与上一命令等效
 > 3rd
```

Bash 使用 `#` 作为注释符号, 注释内容不会被执行

## 输出

### [echo](https://linux.alianga.com/c/echo.html)

```bash
 $ echo <opt> <string>                           # 打印 string 内容, 不激活转义符号
 $ echo -e <string>                              # 激活字符中的转义字符

 $ echo "first \nsecond"
 > first \nsecond

 $ echo -e "first \nsecond"
 > first
 > second
```

注: 更多转义符号浏览 [转义符号](#常用转义符号)

### [printf](https://linux.alianga.com/c/printf.html)

```bash
 $ printf <format string>                        # 格式化输出字符串

 $ printf "%s length is %.3f" "line" "4.53245"   # 字符串格式化
 > line length is 4.523

 $ printf "%-6s _ %s" "left" "right"               # 默认右对齐, 带 - 左对齐, 数字表示占位
 > left   _ right
```

### 输出重定向

Bash 命令正确结果默认输出到标准输出, 错误内容输出到标准错误输出  
可以通过重定向符号 `>`(覆盖写入) `>>`(追加写入) 将输出内容重定向到指定对象

| 标准输出 | 标准错误 |    空     |
| :------: | :------: | :-------: |
|    &1    |    &2    | /dev/null |

```bash
 $ command 2>&1                                  # 将标准错误输出重定向到标准输出
 > zsh: command not found: data

 $ command 2>/dev/null                           # 将错误写入空的虚拟设备, 即不显示错误
 >

 $ command > file                                # 清空文件内容, 命令标准输出写入文件
 $ date > log.txt; cat log.txt                   # date 命令结果写入 log文件, 打印 log 内容验证写入内容
 > Mon Jul  3 23:05:14 CST 2023

 $ command >> file                               # 保留文件内容, 命令标准输出追加入文件
 $ date >> log.txt; cat log.txt                  # date 命令结果追加入 log文件, 打印 log 内容验证写入内容
 > Mon Jul  3 23:05:14 CST 2023
 > Mon Jul  3 23:05:38 CST 2023

 $ command > /dev/null                           # 命令标准输出写入 null, 即不显示正常结果
 $ date >/dev/null
 >
```

## 变量

BASH 变量默认为字符串, 想要操作字符串或者引用变量需要使用 `$` 符号

```bash
 $ <variable name>=<variable value>              # 变量定义

 $ variable="hello world"                        # 变量赋值, '=' 号前后不允许空格
 $ temp=hello_world                              # 字符串无引号赋值时不允许空格

 $ echo variable                                 # 把打印内容当做字符串直接打印
 > variable

 $ echo $variable                                # 打印变量的值
 > hello world

 $ echo say_${temp}_to_code                      # 字符连用时, 用 {} 区分变量
 > say_hello_world_to_code
```

建议字符串操作时, 添加引号使变量操作更加明确(易于区分编辑器中字符串与变量)

### 环境变量

在打开 BASH 时间, 有一些环境变量提前在 shell 中定义, 这些变量往往有特殊含义

```bash
 $ env                                           # 显示所有环境变量

 $ echo "path: $HOME, user: $USER"                # 打印用户目录和用户
 > path: /root, user: root

 $ echo $PATH                                    # 运行环境目录组成的列表
 $ echo $PWD                                     # 打印当前目录

 $ export <var>=<val>                            # 自定义当前命令行环境变量, 新开窗口或重启失效
 $ unset <var>                                   # 删除变量, 环境变量和自定义变量均可
 $ set                                           # 显示所有 shell 变量
```

注: 更多环境变量浏览 [环境变量](#常用环境变量)

### 特殊变量

```bash
 $ $<char>                                       # 如 $? $! 等特殊含义变量

 $ date;echo "date command return: $?"           # 上个命令返回值
 > Sat May  4 10:11:22 PM CST 2024
 > date command return: 0                        # 0 表示命令执行成功, 其它表示失败或错误

 $ echo "Process ID: $$"                         # 当前 shell 进程 ID
 > Process ID: 12345                             # 12345 为当前 shell 进程 ID
```

注: 更多特殊变量 [特殊变量](#特殊变量)

### 变量默认值

```bash
 $ ${variable:-value}                            # 变量为空或不存在返回 value
 $ ${variable:+value}                            # 变量存在且不为空返回 value

 $ ${variable:=value}                            # 变量为空或不存在设置变量为 value, 返回 values
 $ ${variable:?message}                          # 变量为空或不存在打印 message, 并退出

 $ echo "hello ${name:-petter}"
 > hello petter
```

### 特殊类型变量

```bash
 $ declare <opt> <var>=<val>                     # 定义特殊类型变量

 $ declare -i int1=8 int2=6 sum                  # 定义整数变量, 可直接数学运算
 $ sum=int1+int2; echo $sum                      # 声明整数变量可直接数学运算( + 左右不能有空格)
 > 14

 $ declare -a arr=(1 2 3)                        # 定义数组变量
```

```bash
 $ let <var>=<expression>                        # 声明含算术表达式的变量

 $ let "sum = 7 + 8"
 $ let "add = sum++"
 $ echo "sum: $sum  add: $add"
 > sum: 15 add: 16
```

## 引号

### 单引号与双引号

BASH 区分单引号和双引号, 单引号内全部为当做字符, 双引号会激活变量引用

```bash
 $ name="petter"

 $ echo 'hello\t$name'                           # 单引号禁止变量名扩展, 原样打印
 > hello\t$name                                  # bash 默认不转义 \t, zsh 默认转义 \t

 $ echo "hello\t$name"                           # 双引号允许变量名扩展
 > hello\tpetter
```

### 反引号

反引号用于执行命令, 一般用于将命令结果赋值给变量

```bash
 $ echo date, `date`
 > date, Sat May 4 10:16:10 PM CST 2024          # 反引号会执行包含的内容

 $ echo date, $(date)                            # 与反引号效果一致
 > date, Sat May 4 10:18:29 PM CST 2024
```

由于反引号易于与单引号混淆, 建议使用 `$()` 方式, 便于区分

## 附录

### 常用转义符号

| 符号 |      含义      |
| :--: | :------------: |
| `\n` |     换行符     |
| `\r` | 回车, 回到行首 |
| `\t` |     Tab 键     |
| `\b` | 光标左移 1 位  |

### 常用环境变量

|  变量   |       含义       |
| :-----: | :--------------: |
| `HOME`  |   当前用户目录   |
| `USER`  |     当前用户     |
| `SHELL` | 当前使用的 shell |
| `PATH`  |     环境变量     |
|  `PWD`  |     当前目录     |

### 常用特殊变量

| 变量 |           含义           |
| :--: | :----------------------: |
| `$?` |    上一个命令的返回值    |
| `$!` |     上一个命令的 pid     |
| `$$` |     当前 shell 进程      |
| `$@` | 当前函数或脚本的所有参数 |
