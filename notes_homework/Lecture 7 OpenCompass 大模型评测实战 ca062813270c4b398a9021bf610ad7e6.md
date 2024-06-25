# Lecture 7: OpenCompass 大模型评测实战

## 笔记

- OpenCompass 特点
    - 开源可复现：提供公平、公开、可复现的大模型评测方案
    - 全面的能力维度：五大维度设计，提供 70+ 个数据集约 40 万题的的模型评测方案，全面评估模型能力
    - 丰富的模型支持：已支持 20+ HuggingFace 及 API 模型
    - 分布式高效评测：一行命令实现任务分割和分布式评测，数小时即可完成千亿模型全量评测
    - 多样化评测范式：支持零样本、小样本及思维链评测，结合标准型或对话型提示词模板，轻松激发各种模型最大性能
    - 灵活化拓展：想增加新模型或数据集？想要自定义更高级的任务分割策略，甚至接入新的集群管理系统？OpenCompass 的一切均可轻松扩展！
- OpenCompass 评测对象
    - 基座模型和对话模型
- OpenCompass 评测能力范围
    - 通用能力和特色能力两个方面来进行评测维度设计。在模型通用能力方面，从语言、知识、理解、推理、安全等多个能力维度进行评测。在特色能力方面，从长文本、代码、工具、知识增强等维度进行评测
- OpenCompass 评测方法
    - 采用客观评测与主观评测两种评测方式。客观评测能便捷地评估模型在具有确定答案（如选择，填空，封闭式问答等）的任务上的能力，主观评测能评估用户对模型回复的真实满意度，OpenCompass 采用基于模型辅助的主观评测和基于人类反馈的主观评测两种方式
- 支持的工具
    - OpenCompass 提供丰富的功能支持自动化地开展大语言模型的高效评测。包括分布式评测技术，提示词工程，对接评测数据库，评测榜单发布，评测报告生成等诸多功能。

## 作业

### 使用 OpenCompass 评测 internlm2-chat-1_8b 模型在 C-Eval 数据集上的性能

![Untitled](Lecture%207%20OpenCompass%20%E5%A4%A7%E6%A8%A1%E5%9E%8B%E8%AF%84%E6%B5%8B%E5%AE%9E%E6%88%98%20ca062813270c4b398a9021bf610ad7e6/Untitled.png)

![Untitled](Lecture%207%20OpenCompass%20%E5%A4%A7%E6%A8%A1%E5%9E%8B%E8%AF%84%E6%B5%8B%E5%AE%9E%E6%88%98%20ca062813270c4b398a9021bf610ad7e6/Untitled%201.png)

![Untitled](Lecture%207%20OpenCompass%20%E5%A4%A7%E6%A8%A1%E5%9E%8B%E8%AF%84%E6%B5%8B%E5%AE%9E%E6%88%98%20ca062813270c4b398a9021bf610ad7e6/Untitled%202.png)

![Untitled](Lecture%207%20OpenCompass%20%E5%A4%A7%E6%A8%A1%E5%9E%8B%E8%AF%84%E6%B5%8B%E5%AE%9E%E6%88%98%20ca062813270c4b398a9021bf610ad7e6/Untitled%203.png)