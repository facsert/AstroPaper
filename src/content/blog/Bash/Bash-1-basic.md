---
author: facsert
pubDatetime: 2022-08-09 21:20:45
title: BASH Basic
postSlug: ""
featured: false
draft: false
tags:
  - bash
description: "Bash basic"
---

<!--
 * @Author       : facsert
 * @Date         : 2022-08-09 21:20:45
 * @LastEditTime : 2023-07-28 11:57:23
 * @Description  : edit description
-->

[BASH 教程](https://wangdoc.com/bash/)

BASH 基础语法

```bash
 $ command <option> <args>                       # shell 命令一般格式

 $ cat -n log.txt                                # 读取 log.txt 文件, 命令 cat, 选项 -n, 参数 log.txt
 > 1 1st
 > 2 2nd
 > 3 3rd

 $ command | <command> | <command>               # 命令管道
 $ cat log.txt | grep "3rd"                      # 管道前的值作为管道后命令的参数
 > 3rd
```

## 打印输出

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

 $ printf "%-6s_%s" "left" "right"               # 默认右对齐, 带 - 左对齐, 数字表示占位
 > left  _rightf
```

## 变量引用

BASH 只有字符串一种数据类型, 想要操作字符串或者引用变量需要使用特殊符号

```bash
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

## 引号

### 单引号与双引号

BASH 区分单引号和双引号, 单引号内全部为当做字符(转义符有效), 双引号会激活变量引用

```bash
 $ name="petter"

 $ echo 'hello\t$name'                           # 单引号禁止变量名扩展, 原样打印
 > hello  $name

 $ echo "hello\t$name"                           # 双引号允许变量名扩展
 > hello  petter
```

### 反引号

反引号用于执行命令, 一般用于将命令结果赋值给变量

```bash
 $ time=`date`                                   # 将 data 命令结果赋值给 time
 $ echo "$time"                                  # 打印变量值
 > Wed Aug 10 21:49:38 HKT 2022

 $ time=$(date)                                  # 与反引号效果一致
```

由于反引号易于与单引号混淆, 建议使用 `$()` 方式, 便于区分

## 变量

### 环境变量

BASH 环境中的变量, 系统自定义的全局变量, 变量名全为大写, 用于保存环境信息

```bash
 $ env                                           # 显示所有环境变量

 $ echo $HOME                                    # 打印用户目录
 > /home/facser

 $ echo $PATH                                    # 运行环境目录组成的列表

 $ export <var>=<val>                            # 自定义环境变量, 新开窗口或重启失效
 $ unset <var>                                   # 删除变量, 环境变量和自定义变量均可
 $ set <var>                                     # 显示所有 shell 变量
```

注: 更多环境变量浏览 [环境变量](#常用环境变量)

### 自定义变量

```bash
 $ <var>=<val>                                   # 变量定义, 等号两边不允许空格

 $ $<char>                                       # 如 $? #! 等特殊含义变量
 $ echo $?                                       # 上个命令返回值
 > 0                                             # 0 表示命令执行成功, 其它只表示失败或错误
```

注: 更多特殊变量 [特殊变量](#特殊变量)

```bash
 $ ${variable:-value}                            # 变量为空或不存在返回 value
 $ ${variable:+value}                            # 变量存在且不为空返回 value

 $ ${variable:=value}                            # 变量为空或不存在设置变量为 value, 返回 values
 $ ${variable:?message}                          # 变量为空或不存在打印 message, 并退出

 $ echo "hello ${name:-petter}"
 > hello petter
```

```bash
 $ declare <opt> <var>=<val>                     # 定义特殊类型变量

 $ declare -i int1=8 int2=6 sum                  # 定义整数变量, 可直接数学运算
 $ sum=int1*int2; echo $sum                      # 声明整数变量可直接数学运算
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

## 输出重定向

```bash
 $ command 2>&1                                  # 1 表示标准输出 2 表示标准错误输出
 > zsh: command not found: data

 $ command 2>/dev/null                           # 将错误写入 null, 即不显示错误
 >

 $ command > file                                # 清空文件内容, 命令标准输出写入文件
 $ date > log.txt; cat log.txt
 > Mon Jul  3 23:05:14 CST 2023

 $ command >> file                               # 保留文件内容, 命令标准输出追加入文件
 $ date >> log.txt; cat log.txt
 > Mon Jul  3 23:05:14 CST 2023
 > Mon Jul  3 23:05:38 CST 2023

 $ command > /dev/null                           # 命令标准输出写入 null, 即不显示正常结果
 >
```

## 常用转义符号

| 符号 |      含义      |
| :--: | :------------: |
| `\n` |     换行符     |
| `\r` | 回车, 回到行首 |
| `\t` |     Tab 键     |
| `\b` | 光标左移 1 位  |

## 常用环境变量

|  变量   |       含义       |
| :-----: | :--------------: |
| `HOME`  |   当前用户目录   |
| `USER`  |     当前用户     |
| `SHELL` | 当前使用的 shell |
| `PATH`  |     环境变量     |
|  `PWD`  |     当前目录     |

## 特殊变量

| 变量 |           含义           |
| :--: | :----------------------: |
| `$?` |    上一个命令的返回值    |
| `$!` |     上一个命令的 pid     |
| `$$` |     当前 shell 进程      |
| `$@` | 当前函数或脚本的所有参数 |
