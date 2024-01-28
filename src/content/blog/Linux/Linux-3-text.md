---
author: facsert
pubDatetime: 2022-07-19 15:28:43
title: Linux Text
slug: Linux Text
featured: false
draft: false
tags:
  - Linux
description: "Linux 文本操作"
---

<!--
 * @Author       : facsert
 * @Date         : 2022-07-19 15:28:43
 * @LastEditTime : 2023-10-08 21:32:25
 * @Description  : edit description
-->

## Table of Contents

## 文本搜索

读取文本内容搜索或刷选符合条件的内容

### [grep](https://linux.alianga.com/c/grep.html)

> global search regular expression and print out the line

文本搜索和筛选

```bash
 $ grep <patten> <file>                          # 从文件中筛选出符合模式的行, 可搜索多个文件
 $ egrep <regex> <file>                          # 与 grep -E 类似

 $ grep "3r" host.txt                            # grep 会显示所有包含 “3r” 的行
 > 3rd

 $ cat host.txt | grep "3r"                      # 与上述命令效果一致
 > 3rd

 $ grep -nE "[0-9]th" host.txt                   # 通过正则表达式匹配, 并显示行
 > 4:4th
 > 5:5th

 $ cat host.txt | egrep -i "[0-9]TH" | grep -v 5 # 多次管道筛选行, egrep 与 grep -E 一致
 > 4th                                           # 通过正则忽略大小写筛选, 去除包含 5 的行
```

| parameter | meaning                                                    |
| :-------: | :--------------------------------------------------------- |
|    `c`    | `--count` 只显示匹配行的数量                               |
|    `E`    | `--extend-regexp` 使用正则匹配                             |
|    `i`    | `--ignore-case` 忽略大小写                                 |
|    `o`    | `--only-matching` 只显示匹配到内容, 同行其它内容不显示     |
|    `n`    | `--line-number` 同时显示行数                               |
|    `v`    | `--revert-match` 反转查找, 显示不匹配的所有行              |
|    `w`    | `--word-regexp` 单词全匹配, 存在该单词的行, 不包含子字符串 |
|    `x`    | `--line-regexp` 行全匹配, 必须与行完全一致                 |

### [wc](https://linux.alianga.com/c/wc.html)

Word count 文字计数

```bash
 $ wc -c                                         # --bytes char 统计字符数量
 $ wc -w                                         # --words 统计单词数量
 $ wc -l                                         # --lines 统计行的数量

 $ cat host.txt | wc -l                          # wc 是以行尾是否有换行符号判断为一行
 > 4 host.txt                                    # 实际有 5 行, 第 5 行结尾没有换行符号
```

## 文本编辑

### [xargs](https://linux.alianga.com/c/xargs.html)

extended arguments: 文本格式转换与扩充

```bash
 $ <command> | xargs <options> <command>         # 处理左边命令的输出, 并作为右边命令的输入执行

 $ echo "end" | xargs echo "start"               # 将 "end" 传递给右边 echo 命令
 > start end                                     # 与 echo "start" "end" 一致

 $ cat host.txt | xargs -n 3                     # 读取文本内容, 修改格式, 每行 3 个字符串
 > 1st 2nd 3rd                                   # xargs 右边不填命令, 默认使用 echo
 > 4th 5th

 $ echo "1-2-3-4" | xagrs -d '-' -n 2            # 以 '-' 分割字符串, 分隔符号可以是单个字符,单个数字或单个字母
 > 1 2                                           # 每行显示两个字符串
 > 3 4

 $ echo "1 2 3"| xargs -n1 echo line             # xargs -n1, 每行一个字符串, 将 1 行分割为 3 行
 > line 1
 > line 2
 > line 3

 $ echo "1 2 3"| xargs -n 1 | xargs -i echo "line {} end"
 > line 1 end                                    # xargs -n 1, 每行一个字符串, 将 1 行分割为 3 行
 > line 2 end                                    # -i 使用 {} 变量替换, 每一行内容替换掉 {} 执行
 > line 3 end

 $ cat host.txt | xargs -I num sh -c 'echo num start; echo num end'
 > 1st start                                     # -I 设置变量 num(可自定义), 供后续多条命令执行
 > 1st end                                       # 执行两次 echo 命令, num 替换为 cat 的输出
 > 2nd start
 > 2nd end
```

### [sort](https://linux.alianga.com/c/sort.html)

文本行排序

```bash
 $ sort <file>                                   # 按每行第首字符的 ACSII 码值顺序排序, 相同则往后一个一个比较

 $ sort host.txt                                 # 按每行字符 ACSII 逐个排序
 > 128-1st-1                                     # 每行开头分别是 1 2 3, 按顺序排序
 > 255-3rd-2
 > 32-2nd-0

 $ sort -n host.txt                              # 根据数值比较, 若是字母开头, 按单个字符的 ACSII 数值比较
 > 32-2nd-0                                      # 每行开头数值是 32 128 255, 根据大小排序
 > 128-1st-1                                     # 若开头是字符, 按单个字符 ACSII 数值排序
 > 255-3rd-2

$ sort -t "-" -k 4 -n host.txt                   # 以 '-' 为分割符号, 取第 4 列按数值排序
 > c-32-2nd-0                                    # -t "-" 以 - 符号分隔行
 > b-128-1st-1                                   # -k 取第 4 列
 > a-255-3rd-2                                   # -n 根据数值排序

 $ sort -r <file>                                # --reverse 反向排序
 $ sort -u <file>                                # --unique 不显示重复的行
```

### [tr](https://linux.alianga.com/c/tr.html)

transform 文本替换 压缩 删除

```bash
 $ tr <option> <parameter>                       # 文本替换, 删除, 合并相邻重复

 $ cat host.txt | tr 'a-z' 'A-Z' | tr "-" "="    # 小写全替换为大写, - 替换为 =
 > 1ST=1
 > 2ND=0
 > 3RD=2

 $ echo "aaccbbcc" | tr -s 'ac'                  # -s 压缩多个连续 a 或多个连续 c 为 1 个
 > acbbc                                         # 仅限于单个字符重复, 且相邻重复才会生效

 $ cat host.txt | xargs | tr -d '0-9'            # 删除文件内所有数字
 > st- nd- rd-                                   # -d 后的内容逐一删除, 即删除 0 1 2 3 4 5 6 7 8 9
```

注: tr 替换或删除时把字符集看做**多个字符**进行操作
如 tr -d 'abc' 表示删除文本中所有 a b c 字符

### [sed](https://linux.alianga.com/c/sed.html)

stream editor 流式编辑

```bash
 $ sed <option> <range> <model> <file>

 > option: 参数
 >   -i: sed 的修改结果写入文件
 >   -n: 输出结果, 与 p 合用只打印修改的行
 >   -e: 串联多个 sed 指令

 > range: 行号或者正则筛选范围
 >   行号: '3' 第三行; '2,5' 2到5行; '1~2' 奇数行; '2~2' 偶数行;
 >   正则: '/[0-9]/' 含有数字的行;  '/1st/,/3rd/' 1st 行和 3rd 之间所有行

 > model: 操作模式
 >   p: 打印模式
 >   d: 删除模式
 >   s: 替换模式
 >   <n>g: 与 s 替换模式合用, 标明替换次数
 >   a\: 行尾追加
 >   i\: 行首插入
```

- select 筛选

```bash
 $ sed <option> <line index> file                # 根据行序号筛选行
 $ sed <option> <regex> file                     # 根据关键字筛选行

 $ sed -n '2,$p' host.txt                        # 截取第二行到最后一行 $ 代表最后一行
 > 2nd-0                                         # 使用 -n 参数和 p 打印符合需求的行
 > 3rd-2

 $ sed -n '/[a-z]d/p' host.txt                   # 筛选包含 字母+d 的 行
 > 2nd-0
 > 3rd-2
```

- delete 删除

```bash
 $ sed <option> <line index> d file                # 根据行序号筛选行
 $ sed <option> <regex> d file                     # 根据关键字筛选行

 $ sed '$d' host.txt                               # 删除最后一行
 > 1st-1                                           # 显示删除后剩余的行, 文件保持不变
 > 2nd-0

 $ sed -i '/0$/d' host.txt                         # 删除以 0 为结尾的行
 > 1st-1                                           # -i 显示删除后剩余的行, 并将修改写入文件
 > 3rd-2
```

- replace 替换

```bash
 $ sed <option> 's/<before>/<after>/<n>g' <file> # 逐行替换, s 替换模式; <n>g 替换 n 次, n 不填则全替换

 $ echo "hi hi ha" | sed 's/hi/ha/g'             # 将 hi 替换为 ha, n 未填, 全替换
 > ha ha ha

 $ echo "hi hi ha" | sed 's:hi:ha:'              # 将 hi 替换为 ha, 没有 g, 只替换一次
 > ha hi ha                                      # 定界符除了 / 也可以用 : , 或 |

 $ sed -i 's/-[0-3]/=end/1g' host.txt && cat host.txt
 > 1st=end                                       # -i, 不显示结果, 将修改写入文件
 > 2nd=end                                       # 支持正则表达式, 将 -1 -2 -3 都替换为 =end
 > 3rd=end                                       # 结尾没有 g, 只替换一次

 $ sed -n 's/nd\|rd/th/gp' host.txt              # -n 和 p 合用打印匹配的行
 > 2th-0                                         # 将 nd 或 rd 替换为 th
 > 3th-2

 $ sed 's/.d/(& or th)/g' host.txt               # 使用 & 做变量替换
 > 1st-1
 > 2(nd or th)-0
 > 3(rd or th)-2
```

- add insert 追加 插入

```bash
 $ sed <option> <regex> <a\ i\> <string> <file>  #

 $ sed '/1st/a\first line' host.txt              # 在包含 1st 的行结尾添加 first line
 > 1st-1                                         # 行结尾是换行符, 追加内容在换行符后, 即下一行
 > first line
 > 2nd-0
 > 3rd-2

 $ sed '/1st/1\first line' host.txt              # 在包含 1st 的行开头添加 first line
 > first line
 > 1st-1
 > 2nd-0
 > 3rd-2
```

### [awk](https://linux.alianga.com/c/awk.html)

```bash
 $ awk 'BEGIN{ commands } pattern{ commands } END{ commands }' <file>

 > BEGIN{ commands }: 操作文本前执行, 选填, 一般用于初始化变量
 > pattern{ commands }: 行操作, 循环体, 对每行执行
 > END{ commands }: 每次执行完行操作后执行, 选填, 一般用于打印行操作结果

 $ awk 'BEGIN{i=0} {i++} END{print i}' host.txt  # 打印文本行
 > 3                                             # 初始化 i, 每遍历一行, i + 1, 最后打印 i

 $ awk '{print $1, $NF, "index:"NR}' host.txt    # 以空格为分隔,打印第 1 列, 最后一列及行序号
 > 1st first index:1                             # $0 表示整行, $<n> 表示第n列, 未指定分隔符号默认空格为分隔
 > 2nd second index:2                            # $NF 表示最后一列 $(NF-<N>) 倒数第 n 列
 > 3rd third index:3                             # NR 表示行序号, 不需要 $

 $ num=2; awk -F '=' -v col=$num  '{print  "line: "NR " column " col": "  $col}' host.txt
 > line: 1 column 2: 1                           # -F "=" 以 = 为分隔符号, 与 'BEGIN{ FS = "=" }' 效果一致
 > line: 2 column 2: 0                           # -v col=$num 从外部添加变量赋值给 col
 > line: 3 column 2: 2                           # "" 内容打印, 变量自动替换, $<n> 取第 n 列

 /dev/sda1  234G  191G   31G  87%   /
 |-------|  |--|  |--|   |--| |-| |--------|
    $1       $2    $3     $4   $5  $6 ($NF)   NR: line index
 |-----------------------------------------|
                    $0
```

- select 筛选

```bash
 $ awk <pattern> { command }

 $ awk 'NR%2==1, $3 ~ /ir/ {print $0}' host.txt  # 通过表达式筛选符合条件的行
 > 1st 1 first                                   # NR%2 == 1, 行序号为 2 的倍数余 1 , 即奇数行
 > 3rd 2 third                                   # $3 ~ /ir/ 即第 3 列包含 ir 字符串

 $ awk '$1 !~ /nd/, /^[1-3]/ {print}' host.txt   # 使用正则筛选行
 > 1st 1 first                                   # $1 !~ /nd/ 第 1 列不包含 nd 字符串
 > 3rd 2 third                                   # ^[1-3] 以数字 1 2 3 为行开头

 $ awk '/sec/, /3rd/ {print}' host.txt           # 通过子字符串截取两子字符之间所有的行
 > 2nd 0 second                                  # sec 在第 2 行
 > 3rd 2 third                                   # 3rd 在第 3 行
```

## 正则表达式

|  char   | meaning                                                                                                  |
| :-----: | :------------------------------------------------------------------------------------------------------- |
|   `^`   | 匹配输入字符串的开始位置, ^start 以字符串 start 开头                                                     |
|   `$`   | 匹配输入字符串的结束位置, $end 以字符串 end 开头                                                         |
|   `.`   | 匹配除"\n"之外的任何单个字符, .                                                                          |
|  `[]`   | 匹配所包含的任意**一个**字符, [fac] 匹配 f a c 中任意单个字符, [a-z] 任意单个小写字母 [0-9] 任意单个数字 |
|  `[^]`  | 负值字符范围。匹配任何不在指定范围内的任意字符, [^fac] 匹配不包含 f a c 中任意单个字符                   |
|  `\|`   | 或匹配, 符号两边均可 [0-9]\|[a-z] 匹配任意单个数字或小写字母                                             |
|  `？`   | 匹配前面的子表达式零次或一次, [0-9]? 匹配 0 个或 1 个数字                                                |
|   `+`   | 匹配前面的子表达式一次或多次, [A-Z]+ 匹配 1 个或多个大写字母                                             |
|   `*`   | 匹配前面的子表达式零次或多次, .\*.txt 匹配任意单个字符出现0次或多次, 即表示任意以 .txt 结尾              |
|  `{m}`  | n是一个非负整数。匹配确定的n次。{3} 固定匹配 3 次                                                        |
| `{m,n}` | m和n均为非负整数，其中n<=m。{1，3} 最少 1 次最多 3 次, {2,} 最少两次, {,4} 最多 4 次                     |
|  `()`   | 匹配pattern并获取这一匹配, 将括号内匹配看成一个整体 （[0-9]=)\* "数字=" 的格式出现任意次                 |

### 正则表达式示例

- 匹配 IPV4

```bash
 $ echo "127.0.0.1 \n255.255.1 \n0.0.0.0 " | egrep -o "([0-9]{1,3}\.){3}[0-9]{1,3}"
 > 127.0.0.1                                     # -o 仅显示匹配内容, [0-9]{1,3} 任意数字出现 1 到 3 次
 > 0.0.0.0                                       # ([0-9]{1,3}\.){3}  匹配格式 3 次, . 经过转义仅表示 . 字符
```
