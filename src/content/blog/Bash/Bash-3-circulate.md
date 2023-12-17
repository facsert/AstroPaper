---
author: facsert
pubDatetime: 2023-10-08 21:50:45
title: 03.Bash Loop
postSlug: ""
featured: false
draft: false
tags:
  - bash
description: "Bash 循环语法"
---

<!--
 * @Author: facsert
 * @Date: 2023-10-08 21:50:45
 * @LastEditTime: 2023-10-09 20:00:18
 * @LastEditors: facsert
 * @Description:
-->

[for 循环](#for-循环)
[循环](#while-循环)

## for 循环

```bash
for variable in list; do
    commands
done

 $ for i in (1 2 3);do echo "index: $i" ; done   # for in 循环
 > index: 1
 > index: 2
 > index: 3

for ((expression1; expression2; expression3)); do
  commands
done

 $ for((i=0; i<3; i+=1)); do echo "line:$i";done # for 循环
 > line:0
 > line:1
 > line:2
```

## while 循环

```bash
while condition; do                              # while 循环
  commands
done

 $ i=0; while [ $i -lt 3 ]; do echo "line:$i"; i=$((i+1)); done
 > line:0
 > line:1
 > line:2
```
