---
title: Obsidian+Hugo+github+cloudflare 工作流
summary: 博客吃灰，回来修整
tags:
  - blog
  - hugo
  - themes
categories:
  - 技术
series:
  - 播客搭建
dir: posts/Obsidian+Hugo+github+cloudflare 工作流
share-hugo: true
date: 2025-06-27T02:04:48+08:00
lastmod: 2025-06-28T02:00:16+08:00
---
# 1. hugo 本地构建
参考文献[主题文档 - 基本概念 - LoveIt](https://hugoloveit.com/zh-cn/theme-documentation-basics/#site-configuration)
## 1.1 hugo 安装
因为我是 macOS，所以使用
```zsh
brew install hugo
```
brew 安装
```zsh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
或使用清华源[homebrew \| 镜像站使用帮助 \| 清华大学开源软件镜像站 \| Tsinghua Open Source Mirror](https://mirrors.tuna.tsinghua.edu.cn/help/homebrew/)进行安装
## 1.2 创建项目
```zsh
hugo new site my_website
cd my_website
```
此处`my_website`为项目名称
## 1.3 安装主题
我使用的主题为[GitHub - dillonzq/LoveIt: ❤️A clean, elegant but advanced blog theme for Hugo 一个简洁、优雅且高效的 Hugo 主题](https://github.com/dillonzq/LoveIt)
为了后期便于上传 github，所以此处要将你的项目初始化为 git 仓库, 并且把主题仓库作为你的网站目录的子模块:
```
git init
git submodule add https://github.com/dillonzq/LoveIt.git themes/LoveIt -b v0.3.0
```
此处指定分支为 `v0.3.0` 截止于 2025 年 6 月 28 日为目前最新稳定版本，主线版本可能需要指定 hugo 版本为 0.145.0 .
### 1.3.1 使用示例文件
将项目中 `themes/LoveIt/exampleSite` 中的所有文件复制到项目文件夹中。
## 1.4 在本地启动网站
使用以下命令启动网站:
```
hugo serve
```
在 `http://localhost:1313` 中就能查看你的项目.如果需要修改内容，请在 `content/posts` 和 `hugo.toml` 中修改,具体修改办法详情[主题文档 - 基本概念 - LoveIt](https://hugoloveit.com/zh-cn/theme-documentation-basics/#site-configuration)