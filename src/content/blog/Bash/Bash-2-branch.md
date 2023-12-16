---
author: facsert
pubDatetime: 2023-07-07 17:44:44
title: Bash Branch
postSlug: ""
featured: false
draft: false
tags:
  - bash
description: "Bash branch"
---

<!--
 * @Author       : facsert
 * @Date         : 2023-07-07 17:44:44
 * @LastEditTime: 2023-12-13 22:51:29
 * @Description  : edit description
-->

## if 分支

if 分支是 shell 中的最常见的分支

```shell
if <expression> ; then                           # expression 返回值为 0 表示条件成立
    commands                                     # 表达式成立才会执行 command
fi

if [[ 3 > 2 ]]; then                             #  3 > 2 条件成立
    echo -e " 3 bigger thean 2\n"                # 执行打印
fi

 $ if date; then echo "true"; fi                 # 命令执行成功也表示条件成立
 > Mon Aug 15 21:03:03 CST 2022                  # date 命令执行成功
 > true                                          # 执行分支代码
```

注: if 分支后的命令或表达式返回 0 表示 true, 条件成立, 其余值均表示 false 不成立

### 多重分支

```shell
if <expression>; then                            # command 返回值为 0 表示条件成立
    commands
elif <expression>; then                          # if 不成立, 判断 elif
    commands
else                                             # if 和 elif 均不成立才执行
    commands
fi

if true; then                                    # 命令执行成功表示条件成立
    echo "first if";
elif true; then                                  # 上个 if 条件不成立才会执行判断
    echo "else if";
else                                             # 上述分支均不成立才会执行
    echo "else";
fi

> first if                                       # 执行第一个成立的 if 条件
```

### 表达式

表达式有很多形式, 数值或字符串比较需要使用括号包含  
Linux 命令也可以作为表达式, 命令返回值 0 为 true, 返回值不为 0 为 false

```shell
test < expression >                              # 常用作 if 判断的表达式, 与 [ expression ] 等价
[ expression ]                                   # 同上, 与 test 等价, 括号内空格是必须的
[[ expression ]]                                 # 较上述额外支持正则, 括号内空格是必须的


if [[ $USER == "root" ]]; then
    echo "user is root"
else
    echo "not user root"
fi

[[ $USER == "root" ]] && echo "user is root"     # &&(且) 表达式为 true 才会执行后半段
[[ $USER == "root" ]] || echo "not user root"    # ||(或) 表达式为 false 才会执行后半段
[[ $? -eq 0 ]] && echo "succ" || echo "fail"     # 同时使用 && 和 ||

 $ true && echo $?                               # true 返回值为 0, 前半部分 true, 继续执行后半段
 > 0
 $ false || echo $?                              # false 返回值 1, 前半部分 false, 继续执行后半段
 > 1
```

注: 单括号需要注意变量引用为空导致命令报错或逻辑错误  
`[ $name == "bash" ]` 当 `$name` 为空执行会报错

### 数值比较

单中括号数值比较不支持 `> < ==` 等符号, 使用 `-eq -ne -lt -le -gt -ge` 代替  
双中括号数值比较支持 `> < ==` 符号也支持 `-eq -ne -lt -le -gt -ge`

```bash
 $ [ 3 -eq 3 ]                                   # equal 数值相等表示 true
 $ [ 3 -ne 3 ]                                   # not equal 数值不相等表示 true
 $ [ 3 -lt 3 ]                                   # less than 小于
 $ [ 3 -le 3 ]                                   # less equal 小于等于
 $ [ 3 -gt 3 ]                                   # greater than 大于
 $ [ 3 -ge 3 ]                                   # greater equal 大于等于

 $ [ 0 -ne 0 ]; echo $?
 > 0

 $ [ 3 > 2 ] && echo "true"                      # 执行命令 "3" 结果写入 2 文件, 执行成功打印 true
 > true
```

注: 数值比较尽量不要使用 `>` `<` `==`等符号, 使用双中括号和字符比较

### 字符串比较

```bash
 $ [ -z "str" ]                                  # 字符串长度为 0 为 true
 $ [ -n "str" ]                                  # 字符串长度不为 0 为 true

 $ [ "abc" == "abc" ]                            # 字符串相等为 true
 $ [ "abc" != "abc" ]                            # 字符串不相等为 true

 $ name=""
 $ [ $name == "linux" ] || echo "no linux"       # name 为空, 从 == 开始执行, 报错
 > bash: [: ==: unary operator expected

 $ [[ $name = "linux" ]] || echo "no linux"
 > no linux
```

### 文件比较

```bash
 $ [ -e file ]                                   # 文件存在为 true
 $ [ -d file ]                                   # 路径存在为 true

 $ [[ ! -e file ]] && touch file                 # 如果 file 不存在则创建 file
 $ [[ ! -d /root/path ]] && mkdir -p /root/path  # 如果路径不存在测创建路径

 $ [ -f file ] && cat file || echo "1st" > file  # file 存在则读取 file, 不存在则创建并写入 "1st"
 > 1st
```

### 正则判断

```bash
[[ expression ]]                                 # 仅双括号支持正则表达式

[[ "abc" =~ "b" ]]                               # true 判断 abc 是否包含 b
[[ "01:01:01" =~ ([0-9]{2}\:){2}[0-9]{2} ]]      # true 正则匹配时间格式, 正则表达式不能用引号包含
[[ "10.0.0.1" =~ ([0-9]{1,3}\.){3}[0-9]{1,3} ]]  # true 正则匹配 IPV4
```

### 逻辑判断

```bash
[ ! expression ]                              # 单括号逻辑非
[[ ! expression ]]                            # 双括号逻辑非

[ true -a true ]                              # true 单括号逻辑与
[[ true && false ]]                           # false 双括号逻辑与

[ false -o true ]                             # true 单括号逻辑或
[[ false || true ]]                           # true 双括号逻辑或
```

### 逻辑运算

```bash
 $ command && command                            # 前一条命令成功才会执行后一条

 $ true && echo "true"                           # true 返回值 0, 继续执行
 > true

 $ ! false && echo "not false"                   # 前一条命令返回值 0, 继续执行
 > not false

 $ command || command                            # 前一条失败后才会继续执行后一条

 $ false || echo "false"                         # false 返回值 1, 执行下一条
 > false
```

## case 分支

```bash
case expression in
    pattern1 )
        commands;;
    pattern2 )
        commands;;
    esac

index=2
case $index in
    1)
        echo "index is 1";;
    2)
        echo "index is 1";;
    3)
        echo "index is 1";;
    *)
        echo "index not in 1,2,3";;
    esac
```
