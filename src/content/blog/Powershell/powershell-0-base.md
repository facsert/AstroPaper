---
author: facsert
pubDatetime: 2024-01-20 11:33:44
title: 01.Powershell Basic
slug: 01.Powershell Basic
featured: false
draft: false
tags:
  - powershell
description: "powershell 基本语法"
---

Powershell 是 windows 上推荐的 shell 脚本语言

## Table of Contents

## 基础

```powershell
# 允许任意 powershell 脚本执行
Set-ExecutionPolicy Unrestricted

# 计算机管理=>服务=>启动 OpenSSH(外部 SSH 进入 windows 默认 shell 设为 powershell)
New-ItemProperty -Path "HKLM:\SOFTWARE\OpenSSH" -Name DefaultShell -Value "C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe" -PropertyType String -Force
```

powershell 使用 `# commit` `<# commit #>` 作为单行和多行注释

```powershell
# powershell 单行注释

<#
  powershell 多行注释
#>
```

powershell 使用反引号作为转义符号

| 符号 | \`n  | \`r | \`t |
| :--: | :--: | :-: | :-: |
| 含义 | 换行 |     |     |

输出重定向

| 符号 |   `>`    |   `>>`   |     `>&1`      | `>null`  |
| :--: | :------: | :------: | :------------: | :------: |
| 含义 | 覆盖写入 | 追加写入 | 输出到标准输出 | 输出置空 |

- 1: 成功流
- 2: 错误流
- 3: 警告流
- \*: 所有流

```powershell
command n>file

Get-Process -Name python 2>&1
```

## 变量

powershell 以 `$` 开头定义变量, 变量是强类型, 对大小写不敏感

```powershell
$str = "a string variable"                       # 定义字符串
$num = 3                                         # 定义数值
$arry = 1,2,3,4                                  # 定义列表
$list = @()                                      # 定义空列表
$hash = @{name="John"; age=18}                   # 定义哈希表

[string]$str = "hello"
[int32]$num = 10
[Object[]]$arr = 3,4,5
[Hashtable]$map = @{a=1; b=2}
```

```powershell
$variable = "hello"
$variable.GetType()                              # 获取变量类型

IsPublic IsSerial Name                                     BaseType
-------- -------- ----                                     --------
True     True     String                                   System.Object

$variable | Get-Member                           # 查看变量的属性和方法

   TypeName:System.String

Name             MemberType            Definition
----             ----------            ----------
Clone            Method                System.Object Clone(), System.Object ICloneable.Clone()
CompareTo        Method                int CompareTo(System.Object value), int CompareTo(string strB), int I...
Contains         Method                bool Contains(string value)
......
......
```

## 数组

```powershell
$empty = @()
$list = 0,1,2,3,4
$array = 0..4

$list[0]
$list[1..3]
$list[-1]

$list.Length
```

## 哈希表

```powershell
$empty = @{}
$hash = @{name="John"; age=18}
```

## 控制语句

```powershell
if ($a -gt $b) {
    # code
} else {
    # code
}

while ($a -eq $b) {
    # code
}

for ($i = 0; $i -lt 10; $i++) {
    # code
}

foreach ($i in $list) {
    # code
}

switch ($value) {
  1 { <# code #>}
  2 { <# code #>}
  3 { <# code #>}
  default { <# code #>}
}
```

### 比较运算符

```powershell
3 -eq 3                                          # True 数字或字符串相等
2 -ne 3                                          # True 数字或字符串不等
3 -gt 2                                          # True 大于
3 -ge 2                                          # True 大于等于
3 -lt 2                                          # False 小于
3 -le 2                                          # False 小于等于

1,2,3 -contains 2                                # True 包含(列表包含)
2 -in 1,2,3                                      # True
"aab" -like "a*"                                 # True 相似匹配

!2                                               # False, 取反
1 -and 2                                         # True, 与
1 -or 0                                          # True, 或
-not 1                                           # True, 非
```

## 函数

```powershell

function myfunc($param1, $param2) {
    # code
}

```

## 特定变量

```powershell
$?                                               # 上一条命令执行结果
$false                                           # False
$true                                            # True
$null                                            # Null
$PID                                             # 进程ID
$PWD                                             # 当前目录
```
