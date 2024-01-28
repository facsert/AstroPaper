---
author: facsert
pubDatetime: 2023-05-23 15:28:43
title: Hexo
slug: Hexo
featured: false
draft: false
tags:
  - Hexo
  - Tool
description: "博客框架 Hexo"
---

<!--
 * @Author       : facsert
 * @Date         : 2023-05-23 15:28:43
 * @LastEditTime : 2023-07-28 11:50:44
 * @Description  : edit description
-->

## Table of Contents

## 介绍

[Hexo 官网](https://hexo.io/zh-cn/)

Markdown 文件 --> Hexo 静态网页 --> Github Page 页面

Hexo 是一个基于 JS 的博客框架, Hexo 可以解析 Markdown 生成静态网页
Github Page 为账户下的仓库提供免费的域名, 显示仓库的静态网页

## 准备

|                   工具                    | 使用                                   |
| :---------------------------------------: | :------------------------------------- |
|       [Github](https://github.com/)       | 注册账户, 并添加 sshkey                |
|         [node](http://nodejs.cn/)         | node 的包管理工具 npm 安装 Hexo 及主题 |
|        [Git](https://git-scm.com/)        | 本地代码上传到 github 的工具           |
| [VS Code](https://code.visualstudio.com/) | 编辑 Hexo 配置或markdown文件           |

## Github 仓库

1. 注册 github 账号, 设置 sshkey (浅谈 Git 中有介绍)
2. 创建仓库, 仓库名固定为 `<user>.github.io` 如 `facser.github.io` (user 指账户名)

```bash
 $ https://<user>.github.io/          # 使用该 url 确认是否创建成功
```

## Hexo 安装

创建文件夹用于保存 Hexo 文件(如在 D 盘创建 blog 文件夹)  
安装 Vscode 编辑器, git 工具, node
使用 Vscode 打开 blog 文件夹, 命令行输入(快捷键: ctrl + ~)

```bash
 $ git --version                  # 查看 git 版本, 检查 git 是否安装成功
 > git version 2.27.0.windows.1

 $ npm -v                         # 查看 npm 版本, 检查 node 是否安装成功
 > 6.14.6
```

安装 hexo

```bash
 $ npm install -g hexo-cli        # 下载安装 hexo 所有的包
 $ npm i hexo-deployer-git        # 下载安装 hexo 的 git 专用包
```

注: 若无法下载 hexo 或速度较慢可修改 npm 源为国内镜像, 命令如下:  
`npm config set registry https://registry.npm.taobao.org/`

## Hexo 本地

vscode 打开 blog 文件夹

```bash
 $ hexo init                      # 生成初始化文件
 $ hexo g                         # 生成静态网页
 $ hexo s                         # 部署静态网页到本地预览
```

注: `INFO  Hexo is running at http://localhost:4000/ . Press Ctrl+C to stop.`
将 `http://localhost:4000/` 复制到浏览器打开即可预览

此时本地 blog 还未绑定 github 域名, 用的也是初始主题

## 网站配置

[Hexo 网站参数](https://hexo.io/zh-cn/docs/configuration)

在目录下的 \_config.yml 中配置网站信息

|    参数     | 描述           |
| :---------: | :------------- |
|    title    | 网站标题       |
|  subtitle   | 网站副标题     |
| description | 网站描述       |
|   author    | 您的名字       |
|  language   | 简中使用 zh-CN |
|  timezone   | 网站时区       |

```yaml
title: facser
subtitle: ""
description: "facser blog"
keywords:
author: facser
language: zh-CN
timezone: ""
```

注: 不填的选项, Hexo 会使用默认值

## Hexo 绑定 github

[hexo 绑定私有 repo](https://hexo.io/zh-cn/docs/github-pages)

编辑 blog 目录下 \_config.yml 文件

```yaml
deploy:
  type: git
  repo: git@github.com:facser/facser.http-github.io.git # 仓库 url
  branch: master # 代码分支
  message: modify git.md another time # commit 内容
```

## Hexo 主题

[hexo 主题库](https://hexo.io/themes/)

选择一款主题下载到本地, 将主题文件夹, 放入目录下的 themes 文件夹中
编辑 \_config.yml, 找到 theme 选项, 替换为下载的主题文件夹名

```yaml
theme: <theme> # themes 文件夹下的主题
```

## 编辑文章

[Hexo 创建文章](https://hexo.io/zh-cn/docs/writing)
[Hexo 添加属性](https://hexo.io/zh-cn/docs/front-matter)

Vscode 打开 blog 文件夹, 命令行输入:

```bash
 $ hexo new post <title>       # 创建文章, title 表示文章标题
```

Hexo 会在 source/\_posts 创建 `<title>.md` 文件

文件生成后, 头部会带有 `title` 及 `data` 信息, 自行选择添加属性

|    参数    | 描述         |
| :--------: | :----------- |
|   title    | 文章大标题   |
|    date    | 文章创建时间 |
|    tags    | 文章标签     |
| categories | 文章分类     |
|  excerpt   | 文章摘要     |

```yaml
---
title: 浅谈 Git
date: 2022-07-10 21:08:54
categories:
  - [tools]
  - [share]
tags:
  - git
excerpt: git 配置及使用
---
```

## Hexo 上传

```bash
 $ hexo clean                  # 清除原有的静态网页
 $ hexo g                      # generate  生成静态网页
 $ hexo d                      # 上传网页到 github 仓库
```

注: 可使用 `hexo s` 命令进行本地预览
