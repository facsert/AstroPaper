---
author: facsert
pubDatetime: 2024-04-29 22:39:27
title: React hook
slug: React hook
featured: false
draft: false
tags:
  - React
description: "React hooks"
---

## Table of Contents

## useState

```ts
import { useState } from 'react';

// 计数器
export default function Counter() {
    const [count, setCount] = useState(0);
    return (
        <button onClick={() => setCount(count + 1)}></button>
    );
}

// 开关
export default function Switch() {
    const [open, setOpen] = useState(true);
    return (
        <button onClick={() => setCount(!open)}></button>
    );
}
```

## useEffect
