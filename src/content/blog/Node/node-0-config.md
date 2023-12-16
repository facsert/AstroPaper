---
author: facsert
pubDatetime: 2023-05-23 15:28:43
title: Node config
postSlug: Node base config
featured: true
draft: false
tags:
  - node
description:
  Some rules & recommendations for creating or adding new posts using AstroPaper
  theme.
---

<!--
 * @Author       : facsert
 * @Date         : 2023-05-23 15:28:43
 * @LastEditTime: 2023-12-15 23:02:39
 * @Description  : edit description
-->

# Node config

## Download and Update

[nodejs 官网](https://nodejs.org/en)

```bash
 $ apt install node                              # apt 下载 node
 $ node -v                                       # 查看 node 版本
 > v18.17.0
```

nodejs 通过 n 模块更新或切换版本

```bash
 $ node -v                                       # node 版本
 > v18.15.0
 $ npm cache clean -f                            # 清除nodejs的cache：
 $ npm install -g n                              # 使用npm全局安装n模块
 $ npm view node versions                        # node所有版本
 $ n latest                                      # 升级到最新版本
 $ n stable                                      # 升级到最新稳定版本
 $ n 18.15.0                                     # 升级到 18.15.0 版本, 某个具体版本号

 $ npm -v                                        # npm 版本, node 的包管理器
 > 9.5.0
 $ npm install npm@latest -g                     # npm 升级到最新版
 $ npm install npm@xx -g                         # npm 升级到指定版本
 $ npm version                                   # 查看当前 npm 版本详情
 $ npm view npm version                          # 查看 npm 最新版本
 $ npm view npm versions                         # 查看 npm 所有版本
 $ npm list                                      # 已安装插件清单
```

### npm 配置

npm 可用更换默认模块安装位置和缓存位置
npm 可更换下载源为国内源

```bash
 $ npm config set prefix <path>                  # 更换全局模块安装位置
 $ npm get prefix                                # 查看 npm 安装全局模块的位置
 > /usr/local

 $ npm config set cache <path>                   # 更换 npm 缓存位置
 $ npm get cache                                 # 查看 npm 缓存存放的位置
 > /Users/facsert/.npm

 $ npm install nrm -g                            # 使用资源切换工具 nrm 切换下载源
 $ nrm ls                                        # 列出可用的下载源
 $ nrm use taobao                                # 使用 taobao 源

 $ npm config get registry                       # 查看当前 npm 下载源
 > https://registry.npmjs.org/                   # 官方 npm 下载源

 $ npm config set registry <source>              # 命令切换 npm 下载源
```

npm 全局安装, 存放在全局模块位置, 所有项目生效
npm 本地安装, 在当前目录 node_modules 文件夹下, 仅目录下项目生效

```bash
 $ npm init                                      # 初始化项目, 生产 package.json 记录安装的模块及版本
 $ npm install vue --save                        # 更新 package.json 中 vue 版本的版本信息
 $ npm install                                   # 目录下存在 package.json 会自动根据文件下载依赖包

 $ npm list                                      # 查看当前项目安装的模块

 $ npm list -g                                   # 查看全局安装的模块
  /usr/local/lib
  ├── corepack@0.17.1
  ├── n@9.0.1
  ├── npm@9.6.2
  └── yarn@1.22.19

 $ npm install <package>                          # 本地安装模块
 $ npm install <package> -g                       # 全局安装模块

 $ npm uninstall <package>                        # 卸载本地安装的模块
 $ npm uninstall <package> -g                     # 卸载全局安装的模块
```

### yarn 配置

```bash
 $ npm install yarn -g                           # npm 全局安装 yarn
 $ yarn config set global-folder <path>          # 设置全局安装目录
 $ yarn config set cache-folder <path>           # 设置全局缓存目录

 $ yarn global add yrm                           # 安装 yrm 以切换下载源
 $ yrm ls                                        # 列出所有下载源
 $ yrm use taobao                                # 使用 taobao 源

 $ yarn config get registry                      # 查看当前 yarn 下载源
 > https://registry.yarnpkg.com                  # yarn 官方默认源

 $ yarn config set registry <source>             # 命令更换 yarn 下载源
```

yarn 的使用与 npm 类似

```bash
 $ yarn init                                     # yarn 初始化项目, 生成 package.json
 $ yarn install                                  # 目录下有 package.json 自动下载文件内的包

 $ yarn global add <package>                     # 全局安装工具包
 $ yarn add <package>                            # 本地安装包

 $ yarn global remove <package>                  # 卸载全局包
 $ yarn remove <package>                         # 卸载项目下的包

 $ yarn upgrade <package>                        # 更新某个包

 $ yarn list                                     # 显示本地安装的所有包

 $ yarn global list                              # 显示全局安装的所有包
 > yarn global v1.22.19
 >   - yrm

 $ yarn info <package>                           # 查看包的信息
```
