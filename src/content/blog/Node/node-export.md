---
author: facsert
pubDatetime: 2023-11-18 18:30:59
title: NodeJS export
slug: NodeJS export
featured: false
draft: false
tags:
  - NodeJS
description: "NodeJS 模块引入和导出"
---

<!--
 * @Author: facsert
 * @Date: 2023-11-18 18:30:59
 * @LastEditTime: 2023-12-15 23:03:36
 * @LastEditors: facsert
 * @Description:
-->

## Table of Contents

早期历史原因, javascript 划分了 commonjs 模块和 EMCAScript 模块, 两种模块的导出和引入方式统统

## Commonjs

- module.exports 创建模块的对外内容
- require 引入其他模块
- 同步加载, 所有模块加载完后执行

CommonJS 模块是 NodeJS 专用, 与 ES6 模块不兼容但 NodeJS 支持 ES6 模块  
NodeJS 默认使用 CommonJS 模块

```js
// module.js
var s = "commonjs varoable";
var num = 3;
var add = n => n + 1;

module.exports = {
  string: s,
  number: num,
  function: add,
};
```

```js
// use.js 引入外部模块内容
const m = require("./module.cjs");
console.log(m.string, m.number, m.function);
```

## EMCAScript

- export 创建模块的对外内容
- import 引入其他模块提供的内容
- 异步加载, 加载完立即执行

NodeJS 要求 ES6 模块文件名必须以 .mjs 结尾, 否则会报错  
若不想改动文件名, 可以在 package.json 文件中配置 `{"type": "module"}`

```js
// 文件 module.mjs 声明可引用内容
var s = "ES6 varoable";
var num = 3;
var add = n => n + 1;

export { s, num, add };
```

```js
// use.mjs 引入外部模块内容
import { s, num, add } from "./module.js";
console.log(s, num, add);
```

`export default` 用于指定模块的默认输出, 一个模块只允许一个默认输出  
import 模块的默认输出无需花括号, 且可以重新命名

```js
// module.mjs 声明可引用内容
var s = "export default varoable";
var num = 3;
var add = n => n + 1;

export default {
  string: s,
  number: num,
  function: add,
};
```

```js
import m from "./use.mjs";
console.log(m);
```
