# Lecture3: "茴香豆":零代码搭建你的 RAG 智能助理

## RAG 概念笔记

RAG (retrieval augmented generation)通过检索和生成的手段利用外部知识库在不改变 LLM 参数的情况下，提升模型的表现。

LLM 固有的挑战有：

1. 生成幻觉（胡言乱语）
2. 过时知识
3. 缺乏可追溯的推理过程

RAG 的出现通过补充额外知识库来解决这些挑战。

RAG 最早在 “Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks” 中提出

RAG 的工作流程：

1. 通过将知识源以合理，适用于场景的粒度向量化存于向量数据库中，为后续流程做准备。
2. 将用户的输入向量化，在向量数据库中检索与其最相关的文档块(top-k chunks)
3. 将用户的输入和检索到的文档块一起作为提示，输入到 LLM 中，生成最终的回答

![Untitled](Lecture3%20%E8%8C%B4%E9%A6%99%E8%B1%86%20%E9%9B%B6%E4%BB%A3%E7%A0%81%E6%90%AD%E5%BB%BA%E4%BD%A0%E7%9A%84%20RAG%20%E6%99%BA%E8%83%BD%E5%8A%A9%E7%90%86%20143dfedf2b294a929c6c89c21bacba34/Untitled.png)

因为涉及到检索过程，所以可以将对传统检索优化的方法也应用到 RAG 中，例如 pre-retrieval 和 post-retrieval. 常见优化方法有：

1. 索引优化(indexing optimization)：以更恰当的粒度划分索引文档和加入元数据
2. 查询优化(query optimization): 查询扩展
3. 向量化优化(embedding optimization)： 对向量的优化
4. 上下文管理(context curation)：重排，上下文选择/压缩
5. LLM 微调：检索微调、生成微调
6. 迭代检索(iterative retrieval)：根据初始查询和迄今为止生成的文本进行重复检索
7. 递归检索(recursive retrieval)：迭代细化搜索查询
8. 自适应检索(adaptive retrieval)：self-rag,使用 LLM 主动决定检索的最佳时机和内容

RAG 与微调(fine-tuning)的对比

RAG 优势：动态知识更新，处理长尾知识问题

RAG 局限：依赖于外部知识库的质量和覆盖范围

微调优势：模型针对特定任务优化

微调局限：需要大量标注数据，对新任务的适应性较差

![Untitled](Lecture3%20%E8%8C%B4%E9%A6%99%E8%B1%86%20%E9%9B%B6%E4%BB%A3%E7%A0%81%E6%90%AD%E5%BB%BA%E4%BD%A0%E7%9A%84%20RAG%20%E6%99%BA%E8%83%BD%E5%8A%A9%E7%90%86%20143dfedf2b294a929c6c89c21bacba34/Untitled%201.png)

![Untitled](Lecture3%20%E8%8C%B4%E9%A6%99%E8%B1%86%20%E9%9B%B6%E4%BB%A3%E7%A0%81%E6%90%AD%E5%BB%BA%E4%BD%A0%E7%9A%84%20RAG%20%E6%99%BA%E8%83%BD%E5%8A%A9%E7%90%86%20143dfedf2b294a929c6c89c21bacba34/Untitled%202.png)

综述文章”Retrieval-Augmented Generation for Large Language Models: A Survey”

## 茴香豆

茴香豆是一个基于 LLM 的领域知识助手，应用了 RAG 来理解特定领域知识，对 IM 进行了优化和提供自动问答服务。

茴香豆工作流

![Untitled](Lecture3%20%E8%8C%B4%E9%A6%99%E8%B1%86%20%E9%9B%B6%E4%BB%A3%E7%A0%81%E6%90%AD%E5%BB%BA%E4%BD%A0%E7%9A%84%20RAG%20%E6%99%BA%E8%83%BD%E5%8A%A9%E7%90%86%20143dfedf2b294a929c6c89c21bacba34/Untitled%203.png)

### InternLM Studio 部署茴香豆

1. 激活相关环境和依赖

```bash
studio-conda -o internlm-base -t InternLM2_Huixiangdou
conda activate InternLM2_Huixiangdou
pip install protobuf==4.25.3 accelerate==0.28.0 aiohttp==3.9.3 auto-gptq==0.7.1 bcembedding==0.1.3 beautifulsoup4==4.8.2 einops==0.7.0 faiss-gpu==1.7.2 langchain==0.1.14 loguru==0.7.2 lxml_html_clean==0.1.0 openai==1.16.1 openpyxl==3.1.2 pandas==2.2.1 pydantic==2.6.4 pymupdf==1.24.1 python-docx==1.1.0 pytoml==0.1.21 readability-lxml==0.8.1 redis==5.0.3 requests==2.31.0 scikit-learn==1.4.1.post1 sentence_transformers==2.2.2 textract==1.6.5 tiktoken==0.6.0 transformers==4.39.3 transformers_stream_generator==0.0.5 unstructured==0.11.2

```

1. 下载要用到的本地模型和茴香豆程序

```bash
# 创建模型文件夹
cd /root && mkdir models

# 复制BCE模型
ln -s /root/share/new_models/maidalun1020/bce-embedding-base_v1 /root/models/bce-embedding-base_v1
ln -s /root/share/new_models/maidalun1020/bce-reranker-base_v1 /root/models/bce-reranker-base_v1

# 复制大模型参数（下面的模型，根据作业进度和任务进行**选择一个**就行）
ln -s /root/share/new_models/Shanghai_AI_Laboratory/internlm2-chat-7b /root/models/internlm2-chat-7b

cd /root
# 克隆代码仓库
git clone https://github.com/internlm/huixiangdou && cd huixiangdou
git checkout ded0551
```

1. 修改茴香豆配置来适用于本地模型

```bash
sed -i '6s#.*#embedding_model_path = "/root/models/bce-embedding-base_v1"#' /root/huixiangdou/config.ini
sed -i '7s#.*#reranker_model_path = "/root/models/bce-reranker-base_v1"#' /root/huixiangdou/config.ini
sed -i '29s#.*#local_llm_path = "/root/models/internlm2-chat-7b"#' /root/huixiangdou/config.ini
```

1. 创建茴香豆语料

```bash
cd /root/huixiangdou && mkdir repodir

git clone https://github.com/internlm/huixiangdou --depth=1 repodir/huixiangdou
```

1. 创建接受问题列表和拒绝问题列表

```bash
cd /root/huixiangdou
mv resource/good_questions.json resource/good_questions_bk.json

echo '[
    "mmpose中怎么调用mmyolo接口",
    "mmpose实现姿态估计后怎么实现行为识别",
    "mmpose执行提取关键点命令不是分为两步吗，一步是目标检测，另一步是关键点提取，我现在目标检测这部分的代码是demo/topdown_demo_with_mmdet.py demo/mmdetection_cfg/faster_rcnn_r50_fpn_coco.py checkpoints/faster_rcnn_r50_fpn_1x_coco_20200130-047c8118.pth   现在我想把这个mmdet的checkpoints换位yolo的，那么应该怎么操作",
    "在mmdetection中，如何同时加载两个数据集，两个dataloader",
    "如何将mmdetection2.28.2的retinanet配置文件改为单尺度的呢？",
    "1.MMPose_Tutorial.ipynb、inferencer_demo.py、image_demo.py、bottomup_demo.py、body3d_pose_lifter_demo.py这几个文件和topdown_demo_with_mmdet.py的区别是什么，\n2.我如果要使用mmdet是不是就只能使用topdown_demo_with_mmdet.py文件，",
    "mmpose 测试 map 一直是 0 怎么办？",
    "如何使用mmpose检测人体关键点？",
    "我使用的数据集是labelme标注的，我想知道mmpose的数据集都是什么样式的，全都是单目标的数据集标注，还是里边也有多目标然后进行标注",
    "如何生成openmmpose的c++推理脚本",
    "mmpose",
    "mmpose的目标检测阶段调用的模型，一定要是demo文件夹下的文件吗，有没有其他路径下的文件",
    "mmpose可以实现行为识别吗，如果要实现的话应该怎么做",
    "我在mmyolo的v0.6.0 (15/8/2023)更新日志里看到了他新增了支持基于 MMPose 的 YOLOX-Pose，我现在是不是只需要在mmpose/project/yolox-Pose内做出一些设置就可以，换掉demo/mmdetection_cfg/faster_rcnn_r50_fpn_coco.py 改用mmyolo来进行目标检测了",
    "mac m1从源码安装的mmpose是x86_64的",
    "想请教一下mmpose有没有提供可以读取外接摄像头，做3d姿态并达到实时的项目呀？",
    "huixiangdou 是什么？",
    "使用科研仪器需要注意什么？",
    "huixiangdou 是什么？",
    "茴香豆 是什么？",
    "茴香豆 能部署到微信吗？",
    "茴香豆 怎么应用到飞书",
    "茴香豆 能部署到微信群吗？",
    "茴香豆 怎么应用到飞书群",
    "huixiangdou 能部署到微信吗？",
    "huixiangdou 怎么应用到飞书",
    "huixiangdou 能部署到微信群吗？",
    "huixiangdou 怎么应用到飞书群",
    "huixiangdou",
    "茴香豆",
    "茴香豆 有哪些应用场景",
    "huixiangdou 有什么用",
    "huixiangdou 的优势有哪些？",
    "茴香豆 已经应用的场景",
    "huixiangdou 已经应用的场景",
    "huixiangdou 怎么安装",
    "茴香豆 怎么安装",
    "茴香豆 最新版本是什么",
    "茴香豆 支持哪些大模型",
    "茴香豆 支持哪些通讯软件",
    "config.ini 文件怎么配置",
    "remote_llm_model 可以填哪些模型?"
]' > /root/huixiangdou/resource/good_questions.json

cd /root/huixiangdou

echo '[
"huixiangdou 是什么？",
"你好，介绍下自己"
]' > ./test_queries.json

```

1. 测试刚才的配置

```bash
# 创建向量数据库存储目录
cd /root/huixiangdou && mkdir workdir 

# 分别向量化知识语料、接受问题和拒绝问题中后保存到 workdir
python3 -m huixiangdou.service.feature_store --sample ./test_queries.json

```

1. 运行茴香豆

```bash
# 填入问题
sed -i '74s/.*/    queries = ["huixiangdou 是什么？", "茴香豆怎么部署到微信群", "今天天气怎么样？"]/' /root/huixiangdou/huixiangdou/main.py

# 运行茴香豆
cd /root/huixiangdou/
python3 -m huixiangdou.main --standalone

```

运行效果截图：

![Untitled](Lecture3%20%E8%8C%B4%E9%A6%99%E8%B1%86%20%E9%9B%B6%E4%BB%A3%E7%A0%81%E6%90%AD%E5%BB%BA%E4%BD%A0%E7%9A%84%20RAG%20%E6%99%BA%E8%83%BD%E5%8A%A9%E7%90%86%20143dfedf2b294a929c6c89c21bacba34/Untitled%204.png)