---
author: facsert
pubDatetime: 2023-07-10 20:17:12
title: C Number
postSlug: ""
featured: false
draft: false
tags:
  - C
description: "C 数字类型"
---

<!--
 * @Author       : facsert
 * @Date         : 2023-05-25 20:17:12
 * @LastEditTime : 2023-07-28 11:46:29
 * @Description  : edit description
-->

## 整形

```c
int main(void) {
    short int short_int = 1;
    int normal_int = 2;
    long int long_int = 3;

    printf("size of short int : %zu\n", sizeof(short_int));
    printf("size of normal int: %zu\n", sizeof(normal_int));
    printf("size of long int  : %zu\n", sizeof(long_int));
}

size of short int : 2
size of normal int: 4
size of long int  : 8
```

sizeof : 返回变量占用的存储大小(单位: 字节)

| 关键字 |          signed          |           unsigned            |
| :----: | :----------------------: | :---------------------------: |
|  含义  | 有符号(包含负数, 默认值) | 无符号(从 0 开始, 不包含负数) |

```c
int num = 2;                                     // 省略 signed, 默认有符号
signed num = 2;                                  // 省略 int, int 可以省略
signed int num = 2;                              // 全写, 与上述两种声明效果一致

unsigned short x;                                // unsigned short int; 省略 int
long y;                                          // unsigned long int; 省略 signed 和 int
```

| 类型                 | 大小             | 范围               |
| :------------------- | :--------------- | :----------------- |
| `signed short int`   | 2 个字节 (16 位) | -(2^15) ~ (2^15)-1 |
| `unsigned short int` | 2 个字节 (16 位) | 0 ~ (2^16)-1       |
| `signed int`         | 4 个字节 (32 位) | -(2^31) ~ (2^31)-1 |
| `unsigned int`       | 4 个字节 (32 位) | 0 ~ (2^32)-1       |
| `signed long int`    | 8 个字节 (64 位) | -(2^63) ~ (2^63)-1 |
| `unsigned long int`  | 8 个字节 (64 位) | 0 ~ (2^64)-1       |

### 最值

C 语言最值的定义 /usr/include/limits.h

```c
/* Minimum and maximum values a `signed short int' can hold.  */
#  define SHRT_MIN      (-32768)
#  define SHRT_MAX      32767

/* Maximum value an `unsigned short int' can hold.  (Minimum is 0.)  */
#  define USHRT_MAX     65535

/* Minimum and maximum values a `signed int' can hold.  */
#  define INT_MIN       (-INT_MAX - 1)
#  define INT_MAX       2147483647

/* Maximum value an `unsigned int' can hold.  (Minimum is 0.)  */
#  define UINT_MAX      4294967295U

/* Minimum and maximum values a `signed long int' can hold.  */
#  if __WORDSIZE == 64
#   define LONG_MAX     9223372036854775807L
#  else
#   define LONG_MAX     2147483647L
#  endif
#  define LONG_MIN      (-LONG_MAX - 1L)

/* Maximum value an `unsigned long int' can hold.  (Minimum is 0.)  */
#  if __WORDSIZE == 64
#   define ULONG_MAX    18446744073709551615UL
#  else
#   define ULONG_MAX    4294967295UL
#  endif
```

引入头文件, 通过变量名使用最值

```c
#include <limits.h>

printf("signed short int min  : %d \n", SHRT_MIN);
printf("signed short int max  : %d \n", SHRT_MAX);
printf("signed int min        : %d \n", INT_MIN);
printf("signed int max        : %d \n", INT_MAX);
printf("signed long int min   : %ld \n", LONG_MIN);
printf("signed long int max   : %ld \n", LONG_MAX);
printf("\n");
printf("unsigned short int max: %d \n", USHRT_MAX);
printf("unsigned int       max: %u \n", UINT_MAX);
printf("unsigned long int  max: %lu \n", ULONG_MAX);

> signed short int min  : -32768
> signed short int max  : 32767
> signed int min        : -2147483648            // 占位符对应类型，也有范围
> signed int max        : 2147483647             // 变量只超出占位符范围会溢出，打印错误值
> signed long int min   : -9223372036854775808   // long 超出 %d 范围，需要使用 long 整形占位符 %ld
> signed long int max   : 9223372036854775807    // %d 的范围即为 signed int 的范围

> unsigned short int max: 65535
> unsigned int       max: 4294967295             // 超出 signed 范围, 使用 %u 无符号整型
> unsigned long int  max: 18446744073709551615   // 超出 signed long 范围, 使用 %lu 无符号长整型
```

```c
printf("long int: %d \n", LONG_MAX);             // long 范围超出 %d 范围
> long int: -1                                   // 溢出, 打印错误
```

## 进制

```c
int bin = 0b10;                                  // 二进制 (低版本编译器不支持)
int oct = 010;                                   // 八进制, 0 开头
int ten = 10;                                    // 十进制
int hex = 0x10;                                  // 十六进制

printf("    oct  dec  hex \n");
printf("bin %2o  %2d  %2x \n", bin, bin, bin);
printf("oct %2o  %2d  %2x \n", oct, oct, oct);   // %o  八进制形式打印
printf("dec %2o  %2d  %2x \n", ten, ten, ten);   // %d 十进制形式打印
printf("hex %2o  %2d  %2x \n", hex, hex, hex);   // %x 十六进制形式打印

>     oct  dec  hex
> bin  2   2   2
> oct 10   8   8
> dec 12  10   a
> hex 20  16  10
```

## 浮点数

浮点数的存储方式与整数不一致，浮点数采用科学计数法存储浮点数

`float = (+/-) (1 + 尾数) * (2^指数)`

|  类型  |  符号位  |    指数位    |    尾数     |
| :----: | :------: | :----------: | :---------: |
| float  | 1 位(31) | 8 位(23-30)  | 23 位(0-22) |
| double | 1 位(63) | 11 位(52-62) | 52 位(0-52) |

指数(8 位): 2 的指数(-128 ~ 127), 决定范围
尾数(23位): 小数点后的数值(0 ~ 1), 决定精度

最大值(符号取正, 尾数接近 1, 指数 127): (+1)(1+1) 2^127 = 2^128
精度: 8388608(2^23), 共 7 位, 第 7 位未能覆盖, 所以仅能保证 6 位

```c
float f = 3;
printf("float %f size: %zu \n", f, sizeof(f));
> float 3.000000 size: 4

printf("float %.20f \n", 0.1+0.2);               // 打印浮点数小数点后 20 位
> float 0.30000000000000004441                   // 浮点数精度问题, 0.1+0.2 != 0.3
```

## 类型转换

数字类型之间可以互相转换, 转换的前后类型包含关系可能会导致精度损失或类型溢出

### 隐式转换

```c
float e = 2.718;
int num = e;                                     // 浮点型转整形
printf("num: %d\n", num);
> num: 2                                         // 小数部分丢失

int num = 32768;
short s_num = num;                               // int 转 short int, signed short int (-32768, 32767)
printf("num: %d\n", s_num);
> num: -32768                                    // 数值溢出, 可能导致意想不到的问题
```

### 显式转换

显式转换: (type)variable

```c
int num = 3;
printf("num: %f\n", (float)num);                 // num 强制转为 float 类型
> num: 3.000000
```

## 布尔值

C99 标准开始添加 stdbool.h 引入了 \_Bool 类型
布尔类型本质还是 0 和 1

```c
#define bool _Bool
#define true 1
#define false 0
```

```c
bool normal;
bool flag = true;
printf("bool normal: %d \n", normal);
printf("bool flag  : %d \n", flag);

> bool normal: 0
> bool flag  : 1
```
