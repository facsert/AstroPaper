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
