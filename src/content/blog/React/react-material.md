---
author: facsert
pubDatetime: 2023-11-20 09:20:27
title: React material
slug: React material
featured: false
draft: false
tags:
  - React
description: "React material"
---

## Table of Contents

## 安装

## theme

[Default Theme](https://mui.com/material-ui/customization/default-theme/)

```js
import { usetheme, createTheme } from "@mui/material/styles";

// 获取组件的默认样式
const defaultTheme = useTheme();
const spanSpace = <span>{`spacing ${defaultTheme.spacing}`}</span>;

// 获取全局样式
const globalTheme = createTheme();

// 打印 drawer 的 zIndex 值 1200
console.log(globalTheme.zIndex.drawer);

// 自定义样式覆盖默认样式
const theme = createTheme({
  palette: {
    primary: {
      main: "#0052cc",
    },
    secondary: {
      main: "#edf2ff",
    },
  },
});
```
