# Lecture 4: XTuner 微调 LLM:1.8B、多模态、Agent

## 笔记

### 核心概念

微调就是在基地模型的基础上通过一定的训练集来进行进一步训练，XTuner 就是微调训练的封装良好的程序，能用许多默认的训练参数，仅可能地暴露给用户少的接口，简化用户的使用。

微调产生的额外模型（LoRA 模型）需要和原模型进行融合，生成最终微调后的模型。

### 主要过程

- 选定、生成数据集
- 选定基底模型
- 基于使用 XTuner 的默认配置，稍微修改配置
- 训练

```bash
# 训练命令
xtuner train /root/ft/config/internlm2_1_8b_qlora_alpaca_e3_copy.py --work-dir /root/ft/train

# 使用 deepspeed 来加速训练
xtuner train /root/ft/config/internlm2_1_8b_qlora_alpaca_e3_copy.py --work-dir /root/ft/train_deepspeed --deepspeed deepspeed_zero2
```

- 根据结果选择没有过拟合的模型
- 模型格式转换(LoRA 模型文件)

```bash
# 创建一个保存转换后 Huggingface 格式的文件夹
mkdir -p /root/ft/huggingface

# 模型转换
# xtuner convert pth_to_hf ${配置文件地址} ${权重文件地址} ${转换后模型保存地址}
xtuner convert pth_to_hf /root/ft/train/internlm2_1_8b_qlora_alpaca_e3_copy.py /root/ft/train/iter_768.pth /root/ft/huggingface
```

- 模型整合

```bash
# 进行模型整合
# xtuner convert merge  ${NAME_OR_PATH_TO_LLM} ${NAME_OR_PATH_TO_ADAPTER} ${SAVE_PATH} 
xtuner convert merge /root/ft/model /root/ft/huggingface /root/ft/final_model
```

- 命令行对话测试

```bash
# 与模型进行对话
# 想要了解具体模型的 prompt-template 或者 XTuner 里支持的 prompt-tempolate，可以到 XTuner 源码中的 xtuner/utils/templates.py 这个文件中进行查找。
xtuner chat /root/ft/final_model --prompt-template internlm2_chat
```

- 网页端交互
    - 通过 InternalLM web demo 的支持

## 作业

### 复现认知小助手

命令行端

![Untitled](Lecture%204%20XTuner%20%E5%BE%AE%E8%B0%83%20LLM%201%208B%E3%80%81%E5%A4%9A%E6%A8%A1%E6%80%81%E3%80%81Agent%2092a1a15ff8f4463f89ab3d28fc50fded/Untitled.png)

web UI

![Untitled](Lecture%204%20XTuner%20%E5%BE%AE%E8%B0%83%20LLM%201%208B%E3%80%81%E5%A4%9A%E6%A8%A1%E6%80%81%E3%80%81Agent%2092a1a15ff8f4463f89ab3d28fc50fded/Untitled%201.png)