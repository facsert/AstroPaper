---
author: facsert
pubDatetime: 2024-02-21 20:34:43
title: Python path
slug: Python path
featured: false
draft: false
tags:
  - Python
description: "Python 文件及路径操作"
---

## Table of Contents

## path

路径或文件判断

```py
import os

os.sep                                           # 系统路径符号, linux 为 "/", windows 为 "\\"

os.path.isabs("/root/temp")                      # True linux 判断路径是否为绝对路径
os.path.isabs("D:\\temp")                        # True windows 判断路径是否为绝对路径

os.path.isdir("/root/temp")                      # False 使用绝对路径, 判断路径是否为目录且路径存在
os.path.isdir("temp")                            # True 相对路径为路径且路径存在

os.path.isfile("temp.txt")                       # True 同 isdir, 判断路径是否为文件且文件存在

os.path.exists("/root/temp")                     # False 判断路径或者文件是否存在
```

路径字符串操作

```py
import os

os.path.abspath("temp.txt")                      # 当前路径和参数拼接(只拼接不做路径判断) join(os.getcwd(), path)
os.path.abspath("/root/temp.txt")                # 参数为绝对路径则直接返回

os.path.split("/root/home/temp.txt")             # ('/root/home', 'temp.txt') 按最后一个路径符号分割
os.path.dirname("/root/home/temp.txt")           # split 分割的前半部分
os.path.basename("/root/home/temp.txt")          # split 分割的前后半部分, 若以路径符号结尾则返回空字符串

os.path.getsize("/root/Desktop/zip")             # 4096 字节, 返回 linux 文件系统块的大小
os.path.getsize("/root/Desktop/temp.txt")        # 7 字节, 返回实际文件大小, 文件不存在则报 FileNotFoundError

os.path.join("root", "temp")                     # root/temp 路径拼接
os.path.join("/root", "temp", "word.txt")        # /root/temp/word.txt 最少一个参数, 可以无限添加参数
os.path.join("root", "/temp")                    # /temp 只保留最小的绝对路径

os.path.splitext("/root/zip/temp.txt")           # ('/root/zip/temp', '.txt') 切割路径和文件后缀
```

创建和删除

```python
import os
import shutil

os.getcwd()                                      # '/root/Desktop/zip' 当前路径
os.listdir(path)                                 # 返回当前路径下的文件列表
os.chidr(path)                                   # 切换当前路径

os.open("file.txt", 'w').close()                 # 打开文件, 文件不存在则创建
os.remove("temp/file.txt")                       # 删除文件, 文件不存在报错 FileNotFoundError

os.mkdir("temp/folder")                          # 创建文件夹, 文件夹已存在或父路径不存在报错
os.makedirs("temp/folder", exist_ok=True)        # 创建多重路径, 路径不存在则创建, 存在也不报错

os.rmdir("temp")                                 # 删除空目录, 目录非空或不存在则报 OSError 或 FileNotFoundError
os.removedirs("temp/folder")                     # 递归删除多重空路径, folder 为空则删除, 之后若 temp 为空也删除
shutil.rmtree("temp/folder")                     # 递归删除目录, 不论文件夹是否为空

os.rename('temp.txt', 'file.txt')                # 重命名文件, 参数是路径报错 OSError

shutil.copy('temp.txt', 'file.txt')              # 复制文件, 参数是路径报错 OSError
shutil.copytree('temp', 'folder')                # 递归复制目录, 目录存在则报错

ignores = lambda dir, files: [f for f in files if f.endswith('.txt')]
shutil.copytree('temp', 'folder', ignore=ignores)# 递归复制目录, 并忽略符合 ignores 函数的文件

for root, dirs, files in os.walk(path)           # 遍历目录, 返回目录路径, 目录列表, 文件列表
    [print(join(root, f)) for f in files]
```
