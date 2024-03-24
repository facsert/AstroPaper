---
author: facsert
pubDatetime: 2024-03-05 17:18:43
title: HTML Base
slug: HTML Base
featured: false
draft: false
tags:
  - HTML
description: "HTML 基础"
---

## Table of Contents

## 介绍

HTML(Hypertext Markup Language) 翻译为超文件标记语言, 用于描述网页的结构和内容

- 语义化: HTML 标签具有语义化, 标签名和作用紧密相关
- 层级结构: HTML 标签具有层级结构, 通过嵌套关系描述网页结构
- 可拓展性: HTML 可通过自定义属性拓展网页内容

```html
<!doctype html>
<!-- 声明文档类型 -->
<html lang="en">
  <!-- 文档根元素 -->
  <head>
    <!-- 文档头部, 包含了元数据和引用的外部资源等信息 -->
    <meta charset="UTF-8" />
    <!-- 字符编码 -->
    <title>HTML5 页面示例</title>
    <!-- 浏览器标题栏内容 -->
  </head>

  <body>
    <!-- 主体可视部分 -->
    <header>
      <!-- 页眉, 包含标题导航等 -->
      <h1>欢迎来到我的网站</h1>
      <nav>
        <ul>
          <li><a href="#">首页</a></li>
          <li><a href="#">关于我们</a></li>
        </ul>
      </nav>
    </header>

    <main>
      <!-- 页面主要内容 -->
      <article>
        <h2>最新文章</h2>
        <p>这是一篇关于HTML5的文章...</p>
      </article>
    </main>

    <footer>
      <!-- 页脚, 版权信息, 外部链接 -->
      <p>&copy; 2024 我的网站</p>
    </footer>
  </body>
</html>
```

## 全局属性

全局属性 (global attributes) 是所有元素都可以使用的属性(有些属性对部分元素无意义)

```html
<!-- id class 均用于选择器定位元素  -->
<div id="id id-name" class="div-class div-name">select html</div>

<!-- title, 鼠标悬停在元素时可见 title 内容 -->
<div title="visible when mouse hover">mouse hover here</div>

<!-- style 可定义 html 内联样式 -->
<div style="color:red;">css in html</div>
```

id
class
title
style

## head

head 用以描述文件的标题, 关键字, 字符编码, 引入外部资源, 通过页面元数据(描述和概括能)可更容易找到相关信息

```html
<head>
  <meta charset="UTF-8" />
  <!-- 设置字符编码为UTF-8 -->
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <!-- 设置移动设备视口 -->
  <meta name="description" content="web site" />
  <!-- 设置页面描述 -->
  <title>web title</title>
  <!-- 设置页面标题 -->
  <link rel="stylesheet" href="styles.css" />
  <!-- 引入外部 CSS 样式 -->
</head>
```
