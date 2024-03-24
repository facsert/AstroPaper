---
author: facsert
pubDatetime: 2024-03-11 16:36:43
title: NodeJS string
slug: NodeJS string
featured: false
draft: false
tags:
  - NodeJS
description: "NodeJS 字符串"
---

## Table of Contents

## 字符串

字符串是 javascrip 中一种基本数据类型

```js
let str = "javascript";

str.length; // 50, 字符串长度

str += "add"; // javascriptadd 字符串拼接
str.concat("concat"); // javascriptcontact 字符串拼接(可拼接多个)

str[1]; // a 按序号取字符
str.substring(0, 5); // javas 字符串切片(左闭右开, 负数取 0, 自调整参数大小顺序, 单个参数则从参数到末尾)
str.slice(5, -1); // scip 字符串切片(左闭右开, 支持负数, 参数错误返回空, 单个参数表示从序号到结尾)

str.indexOf("ava"); // 1 查找第一个匹配的子字符串的位置, 不存在返回 -1
str.lastIndexOf("ava"); // 1 查找最后一个匹配的子字符串的位置, 不存在返回 -1

str.replace("java", "shell"); // shellscript 字符串替换(只替换一次) replaceAll 全替换

str.toUpperCase();
str.toLowerCase();

"ababa".split("a"); // ['a', 'a', 'a'] 字符串分割

"  space  ".trim(); // space, 取出两边空格
```
