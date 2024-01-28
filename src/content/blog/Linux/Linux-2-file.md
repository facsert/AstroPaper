---
author: facsert
pubDatetime: 2022-07-18 15:02:16
title: Linux File
slug: Linux File
featured: false
draft: false
tags:
  - Linux
description: "Linux 文件操作"
---

<!--
 * @Author       : facsert
 * @Date         : 2022-07-18 15:02:16
 * @LastEditTime : 2023-10-08 21:22:48
 * @Description  : edit description
-->

## Table of Contents

## 特殊目录文件

|  位置   | 全称                   | 说明                                         |
| :-----: | :--------------------- | :------------------------------------------- |
| `/etc`  | Configuration Files    | 系统和软件的配置文件                         |
| `/usr`  | Unix Software Resource | 应用程序默认安装位置                         |
| `/dev`  | Device Files           | 系统外围设备                                 |
| `/mnt`  | Mount Directory        | 空目录, 用于临时挂载文件系统                 |
| `/bin`  | User Binaries          | 所有用户可用的基本命令                       |
| `/home` | Home Directories       | 普通用户的主目录                             |
| `/proc` | Process Information    | 虚拟文件系统, 以映射系统与进程在内存中的信息 |

| 位置                       | 说明                                 |
| :------------------------- | :----------------------------------- |
| `/etc/environment`         | 任意用户打开命令行加载, 系统环境变量 |
| `/etc/profile`             | 任意用户打开命令行加载               |
| `/etc/bash.bashrc`         | 任意用户打开命令行加载               |
| `~/.profile`               | 当前用户打开命令行时执行的文件       |
| `~/.bashrc`                | 当前用户打开 bash 命令行时执行的文件 |
| `/etc/ssh/sshd_config`     | ssh 服务配置文件                     |
| `/etc/apt/source.list`     | (Ubuntu) apt 下载源文件              |
| `/var/cache/apt/archives/` | (Ubuntu) apt 下载包安放位置          |
| `/ect/rc.d/rc.local`       | 系统启动时执行的文件(centos, redhat) |

## 文件查看

### [tree](https://www.linuxcool.com/tree)

以树状结构展示指定目录下的所有文件和子目录

```bash
 $ tree <path>                                   # 显示目录的树状层级图

 > Zip/
   ├── targe.tar.gz
   └── main.sh

 > 0 directories, 2 files

 $ tree -a                                       # 显示所有隐藏文件
 $ tree -d                                       # 仅显示目录不显示文件
 $ tree -f                                       # 树状图显示绝对路径
```

### [pwd](https://www.linuxcool.com/pwd)

### [dirs](https://www.linuxcool.com/dirs)

pwd: print working directory
dir: directories

显示当前绝对路径

```bash
 $ pwd                                           # 显示当前路径
 > /root/Desktop

 $ dirs                                          # 显示当前路径
 > /root/Desktop

 $ echo $PWD                                     # 当前路径的全局变量
 > /root/Desktop
```

### [ls](https://www.linuxcool.com/ls)

list files 显示当前路径下的文件及目录

```bash
 $ ls                                            # 显示当前目录下所有文件及目录
 $ ls -a                                         # --all 额外显示隐藏文件及目录
 $ ls -l                                         # 使用长格式显示文集及目录详细信息
 $ ls -R                                         # --recursive 递归显示所有子目录
 $ ls -S                                         # sort 根据文件大小排序

 $ ls -alS                                       # 参数可无顺序合并, 结果按参数显示
 > total 40
 > drwxr-xr-x 10 root root 4096 Feb 27 20:44 .
 > drwx------ 14 root root 4096 Feb 27 20:35 ..
 > drwxr-xr-x  3 root root 4096 Oct 24 20:33 Blog
```

### [file](https://linux.alianga.com/c/file.html)

显示文件信息

```bash
 $ file <file>                                   # 显示文件类型
 $ file main.py
 > main.py: Python script, ASCII text executable # 文件名, 类型, 编码类型
```

## 文件操作

### [cd](https://www.linuxcool.com/cd)

change directory 切换工作路径

```bash
 $ cd <path>                                     # 切换到 <paht> 目录

 $ cd ..                                         # 返回上级目录
 $ cd -                                          # 回到上次所有在目录

 $ cd folder                                     # 目录必须存在, 否则切换失败
 > bash: cd: folder: No such file or directory
```

### [mkdir](https://www.linuxcool.com/mkdir)

make directories 创建目录

```bash
 $ mkdir <dir>                                   # 创建目录
 $ mkdir -v 1st 2nd 3rd                          # 创建多个文件夹, 并显示过程
 > mkdir: created directory '1st'
 > mkdir: created directory '2nd'
 > mkdir: created directory '3rd'

 $ mkdir -p <path>                               # 创建多级目录,无论多级目录是否存在都会创建
 $ mkdir -p 1st/2nd/3rd
 > mkdir: created directory '1st/2nd'
 > mkdir: created directory '1st/2nd/3rd'
```

### [touch](https://www.linuxcool.com/touch)

创建文件

```bash
 $ touch <file> <file>                           # 文件不存在, 创建文件, 文件存在不创建, 可同时创建多个

 $ touch 1st/main.sh                             # 创建时可以添加路径, 路径不存在会报错
 > touch: cannot touch '1st/main.sh': No such file or directory
```

### [rm](https://www.linuxcool.com/rm)

remove 删除文件

```bash
 $ rm <file>                                     # 删除文件, 或删除文件夹
 $ rm -f <file>                                  # --force 强制删除文件, 不询问
 $ rm -r <path>                                  # -R, --recursive 递归删除, 删除文件夹及下所有文件
 $ rmdir <path>                                  # 删除空目录, 目录下有文件或文件夹报错
 > rmdir: failed to remove '1st': Directory not empty
```

### [mv](https://linux.alianga.com/c/mv.html)

move: 移动文件或文件夹

```bash
 $ mv <file|dir> <file|dir>                      # 剪切复制并重新命名, 文件目录均可
 $ mv <file>  <path>/                            # 保存文件名移动

 $ mv -v                                         # --verbose 显示过程, 打印原名及更改后名
 $ mv -f                                         # --force 强制移动, 存在同名则覆盖
 $ mv -n                                         # --no-clobber 存在同名文件则不移动

 $ mv -bv main.sh master.sh                      # 文件重命名, 同名文件修改文件名令 mv 命令得以成功执行
 > renamed 'main.sh' -> 'master.sh' (backup: 'master.sh~')
 > master.sh  master.sh~
```

### [cp](https://wangchujiang.com/linux-command/c/cp.html)

copy 复制文件

```bash
 $ cp <file|dir> <file|dir>                      # 复制黏贴重命名

 $ cp -v                                         # --verbose 显示详细过程
 $ cp -f                                         # --force 强制复制, 存在同名直接覆盖
 $ cp -r                                         # -R, --recursive 递归复制
 $ cp -b                                         # backup 存在同名文件时, 目标文件改名
 $ cp -l                                         # --link 创建源文件的硬链接
 $ cp -s                                         # --symbolic-link 创建源文件的符号链接

 $ cp -v mian.sh main.sh
 > 'mian.sh' -> 'main.sh'

 $ ln <source> <target>                          # 创建文件的硬链接 (使用绝对路径)
 $ ln -s <source> <target>                       # --symbolic 创建文件的符号链接 (使用绝对路径)
```

- 硬链接: 一个数据绑定两个名字, 两名字都删除才无法访问数据
- 符号链接: 数据的"软链接" "快捷方式", 只是一个链接, 不含数据

## 文件查找

### [find](https://wangchujiang.com/linux-command/c/find.html)

查询指定文件

```bash
 $ find <path> <options>                         # 列出在 <path> 下符合参数的文件或目录(绝对路径)

 $ find . -name "*.txt"                          # 在 当前目录查找所有后缀为 .txt 的文件，模式匹配需要双括号 ""
 > ./a.txt
 > ./b.txt

 $ find . -path "*lib*"                          # 在当前目录所有绝对地址中含有 'lib' 的文件或目录
 > ./temp
 > ./temp/a.txt
 > ./temp/b.txt

 $ find temp -regex ".*\.txt$"                   # 当前目录的 temp 文件夹下符合正则表达式的文件
 > temp/a.txt                                    # .* 任意字符任意次, \.txt$ 以 .txt 结尾
 > temp/b.txt

 $ find <dir> <options> -exec <command> {} \;    # 对查找到的文件进行操作
 $ find . -name "*.sh" -exec rm {} \;            # 删除当前目录下后缀为 .sh 文件
 $ find . -name "*.py" -exec mv {} /root \;      # 将当前目录下后缀为 .py 文件移动到 /root

 $ find . -type f -exec sh -c 'echo >{}' \;      # 清空当前目录下所有文件内容
```

## 文件内容

### [cat](https://www.linuxcool.com/cat)

concatenate 读取文件内容

```bash
 $ cat <file>                                    # 读取并打印文件内容
 $ cat -n <file>                                 # 打印文件内容并添加行数
 > 1  1st
 > 2  2nd
 > 3  3rd
```

#### [head](https://www.linuxcool.com/head)

#### [tail](https://www.linuxcool.com/tail)

读取文件部分内容

```bash
 $ head <file>                                   # 显示文件的前 10 行
 $ head -n 5 <file>                              # --lines 显示文件前 5 行
 $ head -c 20 <file>                             # --bytes 显示文件前 20 个字符

 $ tail <file>                                   # 显示文件的后 10 行
 $ tail -n 5 <file>                              # --lines 显示文件后 5 行
 $ tail -c 20 <file>                             # --bytes 显示文件最后 20 个字符

 $ head -c 5 log
 > 1st
 > 2
```
