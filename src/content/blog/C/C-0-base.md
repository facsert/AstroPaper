---
author: facsert
pubDatetime: 2023-07-10 19:20:02
title: C Basic
slug: C Basic
featured: false
draft: false
tags:
  - C
description: "C 语言基本介绍"
---

<!--
 * @Author       : facsert
 * @Date         : 2023-05-25 19:20:02
 * @LastEditTime : 2023-07-28 11:46:18
 * @Description  : edit description
-->
## Table of Contents

## 示例

创建 main.c 文件，写入代码

```c
#include <stdio.h>                               // 引入标准库 stdio.h (/usr/include/)

int main(void) {                                 // 定义执行入口, 入口函数名称固定为 main
    printf("hello world!");                      // 使用 stdio.h 中的 printf 函数
    return 0;
}
```

使用 GCC 编译

```bash
$ gcc main.c -o main                             # 编译 main.c 文件成可执行文件 main
$ ./main                                         # 执行 main
> hello world!                                   # 显示执行结果
```

C 是编译性语言, C 代码需要编译成可执行文件之后才能运行

```bash
main.c  --1--> main.i --2--> main.s --3--> main.o --4--> main

# main.c -> main.i 预处理, 展开头文件，宏替换，全掉注释，条件编译
# main.i -> main.s 编译 优化转化成汇编代码
# main.s -> main.o 汇编转成机器码
# main.o -> main   链接到一起生产可执行文件
```

## 版本

C 版本按照发布时间定义: C89/C90, C99, C11, C17, C2X
C 使用 gcc 编译, 不同 gcc 版本默认使用不同标准编译

```bash
$ gcc -dM -E - < /dev/null | grep "STDC_VERSION" # 查看默认使用的 C 标准

> #define __STDC_VERSION__ 199901L               # 默认使用 C99 标准编译
> #define __STDC_VERSION__ 201112L               # 默认使用 C11 标准编译
> #define __STDC_VERSION__ 201710L               # 默认使用 C17 标准编译
```

强制使用 `-std=<cxx>` 标准编译

```bash
$ cat -n main.c                                  # 写一个 for 循环
> 1  #include <stdio.h>
> 2
> 3  int main() {
> 4      for(int i=0; i < 3; i++) {
> 5          printf("index: %d \n", i);
> 6      }
> 7  }

$ gcc main.c -o main -std=c89                    # 强制使用 C89/C90(同一标准不同叫法) 编译
> main.c: In function ‘main’:
> main.c:5:5: error: ‘for’ loop initial declarations are only allowed in C99 or C11 mode
>    for(int i=0; i < 3; i++) {                  # C89/C90 标准不支持在 for 循环内定义变量
>    ^~~                                         # 提示使用 C99 或以上的标准编译
> main.c:5:5: note: use option -std=c99, -std=gnu99, -std=c11 or -std=gnu11 to compile your code
```

## 格式

```c
int num;                                         // 声明变量 <类型> <变量名>; 分号是必须的
num = 1;                                         // 变量必须声明后才可以赋值

int num = 2;                                     // 变量声明并赋值, 等号两边空格不是必须

int                                              // 语句以分号结尾, 编译器会忽略换行
num
=
3;
```

```c
int add (int x, int y) {                         // <return type> <function name> (<arg type> <arg name>) {
    int sum = x + y;                             // code block
    return sum;                                  // code block
}                                                // }  function end
```

## 关键字

```c
branch:    if     else  switch  case  default
loop:      for    do    while
jump:      break  goto  return  continue

Functions: sizeof typedef
Type:      void   int    char   float    double
           union  enum   struct
Modifiers: short  long   signed unsigned
           const  static auto   register extern  volatile
```
