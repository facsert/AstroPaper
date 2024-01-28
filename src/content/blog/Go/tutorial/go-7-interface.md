---
author: facsert
pubDatetime: 2023-03-29 15:28:43
title: 07.Go Interface
slug: 07.Go Interface
featured: false
draft: false
tags:
  - Go
description: "Go 接口"
---

<!--
 * @Author       : facsert
 * @Date         : 2023-03-29 15:28:43
 * @LastEditTime : 2023-07-28 11:40:03
 * @Description  : edit description
-->

## Table of Contents

## 引申

举办一个活动, 参加的人必须是能唱歌和玩游戏, 所以不管男女老幼, 只要能唱歌和玩游戏就应该都能参加  
对于这个活动, 强调的是能唱歌能玩游戏这两种行为, 不关心其余特质和行为  

用 Go 实现的效果

```go
type People struct { name string }               // 定义 People 结构体
func (p people) sing() { ... }                   // 定义结构体方法 sing
func (p people) play() { ... }                   // 定义结构体方法 play

func activate(singer s, player p) {              // 定义函数, 参数为 singer 和 player 类型
    s.sing()
    p.play()
}
```

然而实际上 People 类型不能作为 activate 的参数
People 即使具有 singer 类型和 player 类型的行为, 但由于类型限制, 无法使用

为了解决以上问题产生了接口类型这一概念

```go
type singer interface { sing() }                 // 定义 singer 接口类型, 只要实现 sing() 即可作为 singer 类型使用
type player interface { play() }                 // 定义 player 接口类型, 只要实现 play() 即可作为 player 类型使用

type People struct { name string }               // 定义 People 结构体
func (p people) sing() { ... }                   // 定义结构体方法 sing
func (p people) play() { ... }                   // 定义结构体方法 play

func activate(singer s, player p) {              // 定义函数, 参数为 singer 和 player 类型
    s.sing()
    p.play()
}

human := People{ name: "facser" }                // 实例化 People, People 实现了 sing() play()
activate(human, human)                           // 第一个 human 作为 singer 类型，第二个 human 作为 player 类型
```

接口类型是一个自定义的抽象类型
接口用于定义拥有同样行为的类型
任意其它类型只要包含接口定义的方法， 都可以作为接口类型使用

```go
type <interface name> interface {
    <function name>(<parameter name> <parameter type>) <return type>
}

type app interface {                             // 定义一个 app 类型接口
    open(click int) string                       // 定义 app 类型需要满足的条件
    close(action string) string                  // 任意结构体实现了接口定义的方法就可以作为 app 类型使用
}

type browser struct (                            // 定义 browser 类型结构体
    name string                                  // 定义 browser 结构体属性
)

func (b browser) open(click int) string {        // browser 类型结构体实现 open 方法
    return Sprintf("click %d open %s", click, b.name)
}

func (b browser) close(action string) string {   // browser 类型结构体实现 close 方法
    return Sprintf("use %s close %s", action, b.name)
}

func relax(application app) {                    // 定义函数 relax, 函数参数为 app 接口类型
    Println(application.open(2))                 // 执行 read 方法
    Println(application.close("swipe up"))       // 执行 search 方法
}

chrome := browser{name: "chrome browser"}        // 实例化 chrome, chrome 包含 open close 方法
relax(chrome)                                    // chrome 满足接口条件, chrome 可以当做 app 类型使用
> click 2 open chrome browser
> use swipe up close chrome browser
```
