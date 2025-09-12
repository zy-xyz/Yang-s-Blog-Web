# whisper 微调经历

## 为什么会想做这个项目
最近在听网课，有些老师的这个发音是相当有特色啊，声音开大了吵我耳朵，声音小了，不知道在说什么，相当恼火！于是乎打算生成一下字幕
## 模型选择
目前市面上较火的开源项目是阿里出品的[FunASR](https://github.com/modelscope/FunASR/blob/main/README_zh.md)和[SenseVoice](https://github.com/FunAudioLLM/SenseVoice)，但是其模型规模较小，因此还是选用 whisper-large-v2，其参数量更大，正确率也相对更高。
> 一开始我使用的是 whisper-large-turbo-v3，效果真烂，微调效果也超级差，不推荐使用，调的我心态爆炸
## 模型微调
使用的微调开源项目[yeyupiaoling/Whisper-Finetune](https://github.com/yeyupiaoling/Whisper-Finetune)
- 推荐 miniforge 环境或者 uv 环境部署，python 可用最新版本
-  安装所需的依赖库。

```shell
python -m pip install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple
```

- Windows需要单独安装bitsandbytes。

```shell
python -m pip install https://github.com/jllllll/bitsandbytes-windows-webui/releases/download/wheels/bitsandbytes-0.41.2.post2-py3-none-win_amd64.whl
```
- 数据集可用 aishell.py 下载，也可以用idm 下载，链接在 aishell 中可以找到
如果需要保留微调的模型添加符号的能力需要`--add_pun=true`并 `pip install modelscope`
- 训练命令`python finetune.py` 参数本人推荐在`finetune.py`中修改，可读性更高
## 参数推荐
`finetune.py`可修改参数如下
```python
add_arg("train_data",    type=str, default="dataset/train.json",       help="训练数据集的路径")
add_arg("test_data",     type=str, default="dataset/test.json",        help="测试数据集的路径")
add_arg("base_model",    type=str, default="openai/whisper-tiny",      help="Whisper的基础模型")
add_arg("output_dir",    type=str, default="output/",                  help="训练保存模型的路径")
add_arg("warmup_steps",  type=int, default=50,      help="训练预热步数")
add_arg("logging_steps", type=int, default=100,     help="打印日志步数")
add_arg("eval_steps",    type=int, default=1000,    help="多少步数评估一次")
add_arg("save_steps",    type=int, default=1000,    help="多少步数保存模型一次")
add_arg("num_workers",   type=int, default=8,       help="读取数据的线程数量")
add_arg("learning_rate", type=float, default=1e-3,  help="学习率大小")
add_arg("min_audio_len", type=float, default=0.5,   help="最小的音频长度，单位秒")
add_arg("max_audio_len", type=float, default=30,    help="最大的音频长度，单位秒，不能大于30秒")
add_arg("use_adalora",   type=bool,  default=True,  help="是否使用AdaLora而不是Lora")
add_arg("fp16",          type=bool,  default=True,  help="是否使用fp16训练模型")
add_arg("use_8bit",      type=bool,  default=False, help="是否将模型量化为8位")
add_arg("timestamps",    type=bool,  default=False, help="训练时是否使用时间戳数据")
add_arg("use_compile",   type=bool, default=False, help="是否使用Pytorch2.0的编译器")
add_arg("local_files_only", type=bool, default=False, help="是否只在本地加载模型，不尝试下载")
add_arg("num_train_epochs", type=int, default=3,      help="训练的轮数")
add_arg("language", type=str, default="Chinese", help="设置语言，可全称也可简写，如果为None则训练的是多语言")
add_arg("task",     type=str, default="transcribe", choices=['transcribe', 'translate'], help="模型的任务")
add_arg("augment_config_path",         type=str, default=None, help="数据增强配置文件路径")
add_arg("resume_from_checkpoint",      type=str, default=None, help="恢复训练的检查点路径")
add_arg("per_device_train_batch_size", type=int, default=8,    help="训练的batch size")
add_arg("per_device_eval_batch_size",  type=int, default=8,    help="评估的batch size")
add_arg("gradient_accumulation_steps", type=int, default=1,    help="梯度累积步数")
add_arg("push_to_hub",                 type=bool, default=False, help="是否将模型权重推到HuggingFace Hub")
add_arg("hub_model_id",                type=str,  default=None,  help="HuggingFace Hub上的模型仓库ID")
add_arg("save_total_limit",            type=int,  default=10,  help="只保存最新检查点的数量")
```
推荐修改参数
`use_8bit=true` 
`gradient_accumulation_steps=8`
> `eval_steps` `save_steps` 评估步数 与 保存步数 可随 梯度累积步数 增加而减少（倍数关系）

> 合并模型如果出错可以将 原 `json` 文件复制到合并模型中
