---
author: facsert
pubDatetime: 2023-11-20 09:20:27
title: React Configuration
slug: React Configuration
featured: false
draft: false
tags:
  - React
description: "React 基本配置"
---

<!--
 * @Author       : facsert
 * @Date         : 2023-11-20 09:20:27
 * @LastEditTime : 2023-11-20 10:44:50
 * @Description  : edit description
-->

## Table of Contents

## 安装

```bash
$ mkdir next-demo && cd next-demo
$ npm init -y
$ npm i react react-dom next

$ ls
> node_modules  package.json  package-lock.json

$ cat package.json
{
  "name": "next-app",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "dev": "next",                               # 添加命令, 开发项目
    "build": "next build",                       # 添加命令, 构建项目
    "start": "next start"                        # 添加命令, 启动项目
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "next": "^14.1.4",
    "react": "^18.2.0",
    "react-dom": "^18.2.0"
  }
}
```

项目结构

```bash
next-app/
│
├── app/                                         # 页面文件夹
│   ├── page.js                                  # 主页
│   └── layout.js                                # 页面模板
│   └── login/                                   # 登录页面
│       └── page.js                              # 登录页面主页
│       └── layout.js                            # 登录页面模板
│
├── public/                                      # 静态资源文件夹
│   └── images/                                  # 图片
│   └── favicon.ico                              # 网站图标
│
├── styles/                                      # 样式文件夹
│   ├── globals.css                              # 全局样式
│   └── Home.module.css                          # 主页样式模块
│
├── components/                                  # 组件文件夹
│   └── Header.js                                # 头部组件
│   └── Footer.js                                # 底部组件
│
├── package.json                                 # 项目配置文件
├── .gitignore                                   # Git 忽略文件
└── README.md                                    # 项目说明文件
```

路由

```bash
# app 文件下, 只解析 page 文件作为路由, 其余文件不作为路由
├── app/
│   ├── page.js
│   └── layout.js                                # "/" 主页
│   └── login/
│   │   └── page.js                              # "/login" 登录页面主页
│   │   └── nav.js                               # 不解析
│   └── (content)/                               # 分组文件, 不作为路由
│       └── tag
│           └── page.js                          # "/tag"
│           └── layout.js
│   └── articles/                                # 分组文件, 不作为路由
│       └── [slug]                               # slug 可以为任意值
│           └── page.js                          # "/articles/3" 使用钩子获取路径参数取值渲染
│           └── layout.js
```

使用 vite 构建 React 项目

```bash
npm i vite -g                                    # 下载 vite

npm create vite demo                             # 创建项目
✔ Select a framework: › React
✔ Select a variant: › JavaScript
Scaffolding project in /root/Desktop/React/demo...

Done. Now run:

  cd demo
  npm install
  npm run dev

cd demo && npm install                           # 下载依赖包
npm run dev                                      # 启动项目
```

修改 vite 配置

```js
export default defineConfig({
  server: {
    host: "10.58.14.96", // 修改服务 host
    port: 5173, // 修改服务端口
  },
  // ......
});
```

## Root

```js
// app/page.jsx
export default function RootPage() {
  return <h1>Hello Next.js</h1>
}

// app/layout.jsx, page 内容会作为 children 变量, 嵌入模板
export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>
        {/* Layout UI */}
        <main>{children}</main>
      </body>
    </html>
  )
}
```

## Router

```js
// 使用 next Link 组件跳转页面
import Link from 'next/link'

export default function PageLink({ posts }) {
  return (
    <Link href='/index'>Index Page</Link>
  )
}

// 使用 next useRouter 跳转页面
'use client'

import { useRouter } from 'next/navigation'

export default function Page() {
  const router = useRouter()
  return (
    <button type="button" onClick={() => router.push('/dashboard')}>
      Dashboard
    </button>
  )
}
```
