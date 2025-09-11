---
title: Windows 内存利用率优化
summary: 本文介绍了如何通过PowerShell的MMAgent命令行工具调整Windows系统预加载和内存管理设置，如程序预加载、内存压缩和页组合等，以提升日常使用体验。作者强调了某些设置如MemoryCompression的关闭原因，指出其对性能的影响并不明显。
tags:
  - Windows
  - powershell
  - 内存管理
categories:
  - 技术
series:
  - Windows
dir: posts/Windows 内存利用率优化
share-hugo: true
date: 2025-09-10T23:04:27+08:00
lastmod: 2025-09-11T11:34:02+08:00
---
此优化针对大内存用户
首先可以管理员身份打开[PowerShell]，
运行命令`get-mmagent`查看系统预读取状况，True就是开启False就是关闭：
每一项对应的是什么意思呢？

`ApplicationLaunchPrefetching`程序预加载进内存(内存大就开)

`ApplicationPreLaunch`程序预运行(CPU速度快就开)

`MaxOperationAPIFiles`最大程序预加载进内存的文件数(内存越大开得越多，默认256)(这个就是在C:WindowsPrefetch里面的文件数量，如果你内存非常大，比如16G或32G就可以把这个设置大一些，可以设置从1~8192)只需要运行`set-mmagent`之后输入数字即可

```
Set-MMAgent -MaxOperationAPIFiles 8192
```

`MemoryCompression`内存压缩(CPU速度快就开)

`OperationAPI`(内存大就开，可以让内存预读取非系统的任何程序)

`PageCombining`(让系统合并具有相同内容的内存，可以节省内存，CPU速度快就开)

1. 如果想关闭某选项就使用`Disable-MMAgent`加上项目名称

2. 如果想开启某选项就使用`Enable-MMAgent`加上项目名称