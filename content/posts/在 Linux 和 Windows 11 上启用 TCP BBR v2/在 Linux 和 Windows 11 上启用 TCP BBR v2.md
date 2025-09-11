---
date: 2025-09-10T23:26:07+08:00
lastmod: 2025-09-11T11:37:21+08:00
title: 在 Linux 和 Windows 11 上启用 TCP BBR v2
dir: posts/在 Linux 和 Windows 11 上启用 TCP BBR v2
share-hugo: true
tags:
  - bbrv2
  - windows11
summary: BBR 是 Google 开发的 TCP 拥塞控制算法。它改进了传统拥塞控制算法（如 CUBIC）的缺点，这些算法会降低丢包速度，从而显着提高高丢包环境中的带宽利用率。值得注意的是，拥塞控制算法管理**数据包发送速率**。例如，如果 Google 服务器启用了 BBR，则其上传速度将最大化可用带宽;从用户的角度来看，这会导致更快的下载速度。
---


发布者[考克斯](https://dev.moe/en/author/coxxs)[2023-04-26](https://dev.moe/en/3021)发表于[随机](https://dev.moe/en/category/random-en)

BBR 是 Google 开发的 TCP 拥塞控制算法。它改进了传统拥塞控制算法（如 CUBIC）的缺点，这些算法会降低丢包速度，从而显着提高高丢包环境中的带宽利用率。

值得注意的是，拥塞控制算法管理**数据包发送速率**。例如，如果 Google 服务器启用了 BBR，则其上传速度将最大化可用带宽;从用户的角度来看，这会导致更快的下载速度。

## 众所周知……

许多人只需几个命令即可在他们的 **Linux** 服务器上启用 BBR：

```
echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf
                  
echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf

sysctl -p
```

因此，当从服务器下载文件时，它会利用全部带宽。但是，在上传文件时，速度仍然很慢，因为用于上传的系统仍在运行传统的拥塞控制算法。

如果用户使用的是 Linux 系统，他们可以使用相同的步骤启用 BBR。对于 Windows 系统，从 Windows 11 22H2 及更高版本开始，Microsoft [增加了](https://datatracker.ietf.org/meeting/112/materials/slides-112-iccrg-an-update-on-rledbat-and-bbrv2-00)对 BBRv2 的支持。

## 在 Windows 2 上启用 BBR / BBR v11

打开 PowerShell（以管理员身份），首先检查当前拥塞控制算法：

```
NetTCPSetting | Select SettingName, CongestionProvider
```

然后启用 BBRv2：

```
netsh int tcp set supplemental template=Internet congestionprovider=BBR2

netsh int tcp set supplemental template=InternetCustom congestionprovider=BBR2

netsh int tcp set supplemental template=Datacenter congestionprovider=BBR2

netsh int tcp set supplemental template=DatacenterCustom congestionprovider=BBR2

netsh int tcp set supplemental template=Compat congestionprovider=BBR2
```

> 在这里，您也可以替换为 （BBR v1）。感兴趣的用户可以测试和比较结果。`BBR2``BBR`

**注意：在 Windows 11 23H2 / 24H2 或更高版本上，启用 BBR v2 可能会导致本地 TCP 连接变得无法使用（**例如，导致 adb 卡顿、Steam 失败等**），我们还需要使用以下命令修复本地 TCP 连接。**（[来源](https://moe.one/thread-1300.htm)，[MSDN](https://learn.microsoft.com/en-us/troubleshoot/windows-server/networking/tcpip-performance-known-issues#explanation))

```
netsh int ipv6 set global loopbacklargemtu=disable

netsh int ipv4 set global loopbacklargemtu=disable
```

再次运行该命令以确认它已成功启用。

[![|609x201](https://dev.moe/wp-content/uploads/2023/04/image.png)](https://dev.moe/wp-content/uploads/2023/04/image.png)

## 启用后…

[![](https://dev.moe/wp-content/uploads/2023/04/image-1.png)](https://dev.moe/wp-content/uploads/2023/04/image-1.png)

无需重新启动系统。单线程上传速度从 10Mbps 提高到 30Mbps。OBS 流媒体和网页浏览等场景的体验也得到了改善。

> **如果您在启用 BBR2 后遇到网络相关问题，您还可以使用以下命令恢复默认设置。**
> 
> ```
> netsh int tcp set supplemental template=Internet congestionprovider=CUBIC
> netsh int tcp set supplemental template=InternetCustom congestionprovider=CUBIC
> netsh int tcp set supplemental template=Datacenter congestionprovider=CUBIC
> netsh int tcp set supplemental template=DatacenterCustom congestionprovider=CUBIC
> netsh int tcp set supplemental template=Compat congestionprovider=NewReno
> 
> netsh int ipv6 set global loopbacklargemtu=enable
> netsh int ipv4 set global loopbacklargemtu=enable
> ```
> 
> 恢复后，无需重新启动。当您需要更快的上传时，您可以暂时再次启用 BBR。