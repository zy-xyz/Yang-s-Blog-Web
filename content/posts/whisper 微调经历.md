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
lastmod: 2025-07-29T14:59:29+08:00
---
https://github.com/Purfview/whisper-standalone-win## 为什么会想做这个项目
最近在听网课，有些老师的这个发音是相当有特色啊，声音开大了吵我耳朵，声音小了，不知道在说什么，相当恼火！于是乎打算生成一下字幕
## 模型选择
目前市面上较火的开源模型有：阿里出品的[FunASR](https://github.com/modelscope/FunASR/blob/main/README_zh.md)和[SenseVoice](https://github.com/FunAudioLLM/SenseVoice)，缺点就是large size的模型并未开源，开源的仅有small模型，并不能跟whisper-large-v3-turbo类似的参数相对更大的模型相提并论
## 模型推理
Windows平台：推荐[whisper-standalone-win](https://github.com/Purfview/whisper-standalone-win) ，是已经打包好的独立可执行文件
其他平台：[faster-whisper](https://github.com/guillaumekln/faster-whisper)
## 模型训练
使用项目 [Whisper-Finetune](https://github.com/yeyupiaoling/Whisper-Finetune)，这是一个微调Whisper语音识别模型和加速推理的项目
### 训练环境

| CPU      | Intel i5-14600KF       |
| -------- | ---------------------- |
| 显卡       | NVIDIA RTX 2080 Ti 22g |
| 内存       | 64g                    |
| 系统版本     | Windows 11 24h2        |
| python版本 | Python 3.13.5          |
### 安装环境
建议使用 conda 安装 python 环境[Installing Miniconda - Anaconda](https://www.anaconda.com/docs/getting-started/miniconda/install#linux)
- 安装所需的依赖
```python
python -m pip install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple
pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu128
```
### 准备数据
 [参考此处](https://github.com/yeyupiaoling/Whisper-Finetune#%E5%87%86%E5%A4%87%E6%95%B0%E6%8D%AE)，教程中使用的数据集链接为 [data_aishell.tgz​](https://openslr.elda.org/resources/33/data_aishell.tgz)，建议使用 IDM 通过代理下载，下载完成后放入`Whisper-Finetune\dataset`中
 - 运行`aishell.py`生成如下列格式的训练集和测试集，可选参数 `--add_pun=true` 添加标点符  %%  需要下载 cn-en-common-vocab471067-large模型，并安装modelscope[audio] %%
```json
 {
   "audio": {
      "path": "dataset/0.wav"
   },
   "sentence": "近几年，不但我用书给女儿压岁，也劝说亲朋不要给女儿压岁钱，而改送压岁书。",
   "language": "Chinese",
   "sentences": [
      {
         "start": 0,
         "end": 1.4,
         "text": "近几年，"
      },
      {
         "start": 1.42,
         "end": 8.4,
         "text": "不但我用书给女儿压岁，也劝说亲朋不要给女儿压岁钱，而改送压岁书。"
      }
   ],
   "duration": 7.37
}
```
### 微调模型
训练命令如下：
```python
python finetune.py --base_model=openai/whisper-large-v3-turbo --output_dir=output/
```
作者推荐参数为：
```python
python finetune.py --base_model=openai/whisper-large-v3-turbo --output_dir=output/ --gradient_accumulation_steps=16 --per_device_train_batch_size=4 --per_device_eval_batch_size=4 --use_compile=true
```
- `--gradient_accumulation_steps=16` 梯度累积步数 `16`
- `--per_device_train_batch_size=4` 训练的batch size `4`
- `--per_device_eval_batch_size=4` 评估的batch size `4`
- `--use_compile=true` 使用Pytorch2.0的编译器
### 合并模型
```python
python merge_lora.py --lora_model=output/whisper-large-v3-turbo/checkpoint-best/ --output_dir=models/
```
### 使用Ctranslate2格式模型预测
```python
ct2-transformers-converter --model models/whisper-large-v3-turbo-finetune --output_dir models/whisper-large-v3-turbo-finetune-ct2 --copy_files tokenizer.json preprocessor_config.json --quantization float16
```
