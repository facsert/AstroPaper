---
author: facsert
pubDatetime: 2022-12-31 19:20:23
title: vim
postSlug: ""
featured: false
draft: false
tags:
  - vim
  - Tool
description: vim 是一个使用广泛的文件编辑器
---

## 介绍

vim 是一个使用广泛的文件编辑器，它支持多种编程语言，并且可以与其他程序配合使用。

## 安装

```bash
 $ sudo apt-get install vim
 $ vim --version
 > VIM - Vi IMproved 8.1 (2018 May 18, compiled Dec 07 2023 15:42:49)
 > ......
```

## 配置

使用 `vi /etc/vim/vimrc`, 编辑 vim 配置  
配置文件使用 `"` 作为注释符号

```bash
 set nu           "在左侧行号
 set tabstop=4    "tab 长度设置为 4
 set nobackup     "覆盖文件时不备份
 set cursorline   "突出显示当前行
 set ruler        "在右下角显示光标位置的状态行
 set autoindent   "自动缩进
 set showmode     "显示当前模式
```

## normal 模式

vim 编辑文件默认就是 normal 模式:

- 快速移动
- 删除内容
- 复制内容
- 黏贴内容
- 搜索内容

### 快速移动

```bash
h, j, k, l  左, 下, 上, 右

b, e  当前单词的开头, 结尾
w, ge 下个单词的开头, 上个单词结尾

0, ^, $     行首, 行首(非空白字符), 文档末尾

ctrl + b 向上滚动一屏
ctrl + f 向下滚动一屏

gg, G 文档开头, 文档末尾
```

### 剪切内容

```bash
x     剪切当前字符
X     剪切当前字符前一个
dd    剪切当前行
dw    剪切当前单词
d$    剪切当前行到行尾
d0    剪切当前行到行首

d<n>j 剪切当前行及其后 n 行
d<n>k 剪切当前行及其前 n 行
d<n>w 剪切当前单词及其后 n 个单词
d<n>b 剪切当前单词及其前 n 个单词

p     黏贴剪切板内容
```

### 复制内容

```bash
yy    复制当前行
yw    复制当前单词
y$    复制当前行到行尾
y0    复制当前行到行首
y^    复制当前行到行首(非空白字符)

y<n>j 复制当前行及其后 n 行
y<n>k 复制当前行及其前 n 行
y<n>w 复制当前单词及其后 n 个单词
y<n>b 复制当前单词及其前 n 个单词

p     黏贴剪切板内容(d 或 y 后操作的内容)
```

## insert 模式

## visual 模式

## command 模式

在任意模式下，按下 `:` 键，进入 command 模式，可以执行 vim 命令

### 搜索

```bash
/pattern + enter 搜索匹配项目
n  N             向下搜索下一个匹配项, 向上搜索上一个匹配项

:s/pattern/replace/g  单行全替换
:s/pattern/replace/gi  忽略大小写替换

:%s/pattern/replace/g  全局替换
:%s/pattern/replace/i  全局忽略大小写替换
```
