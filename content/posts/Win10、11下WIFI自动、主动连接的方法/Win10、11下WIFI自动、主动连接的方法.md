---
title: Win10、11下WIFI自动、主动连接的方法
summary: 有时电脑需要同时连接着Lan（有线网络）和Wlan（Wifi、无线网络）。Windows11 / 10在启动之后，只要插着网线Lan保持连接的情况下，Windows不会主动连接Wifi，即使已经设定为自动连接。你不得不每次启动后手动连接Wifi。
tags:
  - Windows
  - windows11
  - WiFi
categories:
  - 技术
share-hugo: true
dir: posts/Win10、11下WIFI自动、主动连接的方法
lastmod: 2025-09-11T17:42:51+08:00
date: 2025-09-11T17:33:32+08:00
---

有时电脑需要同时连接着Lan（有线网络）和Wlan（Wifi、无线网络）。Windows11 / 10在启动之后，只要插着网线Lan保持连接的情况下，Windows不会主动连接Wifi，即使已经设定为自动连接。你不得不每次启动后手动连接Wifi。网上查了下，很多人有同样的疑问，但是几乎找不到任何有效的解决方法（大多是让你打开自动连接、关闭无线网卡的省电选项，这些肯定都试了，都没有用）。最后终于在[http://Bing.com](https://link.zhihu.com/?target=http%3A//Bing.com)的国际版找到了一个英文博客，用添加一个注册表的值完美解决问题。希望能帮到抓狂的你。

解决方法：

1. Win键+R打开，输入regedit，打开注册表编辑器。

2. 找到以下路径：`HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WcmSvc\GroupPolicy`。

3. 在GoupPolicy下，右侧空白区域“鼠标右键——新建——DWORD（32位）值”。

4. 把这个新建的DWORD(32位)改名为“`fMinimizeConnections`”。

5. 关闭注册表编辑器，重启电脑。搞定。

此方法的原作者网页：[Title Unavailable \| Site Unreachable](https://www.ascinc.com/blog/networking/how-to-connect-to-wifi-automatically-after-restart-when-lan-is-being-plugged-in-windows-10/)

PlanB：稍加完善：编辑组策略→计算机配置→管理模块→网络→Windows连接管理器→最小化到Internet或Windows域的同时连接数→（右键）编辑→已启用→最小化策略选项→0=允许同时连接。