---
title: whisper 微调经历
summary: whisper 模型经过公开数据集微调后效果并不完美，等咱有空手搓一个数据集
tags:
  - whisper
  - Fine-tune
categories:
  - 技术
dir: posts/whisper 微调经历
share-hugo: true
date: 2025-06-27T02:04:48+08:00
lastmod: 2025-07-23T23:35:56+08:00
---
## 为什么会想做这个项目
最近在听网课，有些老师的这个发音是相当有特色啊，声音开大了吵我耳朵，声音小了，不知道在说什么，相当恼火！于是乎打算生成一下字幕
## 模型选择
目前市面上较火的开源模型有：阿里出品的[FunASR](https://github.com/modelscope/FunASR/blob/main/README_zh.md)和[SenseVoice](https://github.com/FunAudioLLM/SenseVoice)
