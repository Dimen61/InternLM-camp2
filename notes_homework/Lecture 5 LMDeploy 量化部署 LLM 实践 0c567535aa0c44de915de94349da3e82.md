# Lecture 5: LMDeploy 量化部署 LLM 实践

## 笔记

- 大模型部署的挑战：
    - 计算量巨大
    
    ![Untitled](Lecture%205%20LMDeploy%20%E9%87%8F%E5%8C%96%E9%83%A8%E7%BD%B2%20LLM%20%E5%AE%9E%E8%B7%B5%200c567535aa0c44de915de94349da3e82/Untitled.png)
    
    - 内存开销巨大
    
    ![Untitled](Lecture%205%20LMDeploy%20%E9%87%8F%E5%8C%96%E9%83%A8%E7%BD%B2%20LLM%20%E5%AE%9E%E8%B7%B5%200c567535aa0c44de915de94349da3e82/Untitled%201.png)
    
    - 访存瓶颈
    
    ![Untitled](Lecture%205%20LMDeploy%20%E9%87%8F%E5%8C%96%E9%83%A8%E7%BD%B2%20LLM%20%E5%AE%9E%E8%B7%B5%200c567535aa0c44de915de94349da3e82/Untitled%202.png)
    
- 部署模型常用的优化方法
    - 模型剪枝
    - 知识蒸馏：训练引导出一个更加轻量的学生模型
    - 量化：将浮点运算转化为整数进行存贮
- LMDeploy 简介：全套的模型轻量化、部署和服务的解决方案
    - 高效推理：continusous batch, blocked k/v cache, 张量并行
    - 可靠量化：支持权重和 k/v 的量化
    - 支持在多机、多卡上进行推理服务
    - 有状态推理：通过缓存多轮对话中 attention 的 k/v, 记住对话历史，从而避免重复计算历史对话
    - 支持多种模型
- LMDeploy 核心功能
    - 高效推理
    
    ```bash
    lmdeploy chat -h
    ```
    
    - 量化压缩
    
    ```bash
    lmdeploy lite -h
    ```
    
    - 服务部署
    
    ```bash
    lmdeploy serve -h
    ```
    
    ## 作业
    
    ### 基础作业
    
    - 配置 LMDeploy 运行环境
    
    ![Untitled](Lecture%205%20LMDeploy%20%E9%87%8F%E5%8C%96%E9%83%A8%E7%BD%B2%20LLM%20%E5%AE%9E%E8%B7%B5%200c567535aa0c44de915de94349da3e82/Untitled%203.png)
    
    - 以命令行方式与 InternLM2-Chat-1.8B 模型对话
    
    ![Untitled](Lecture%205%20LMDeploy%20%E9%87%8F%E5%8C%96%E9%83%A8%E7%BD%B2%20LLM%20%E5%AE%9E%E8%B7%B5%200c567535aa0c44de915de94349da3e82/Untitled%204.png)
    
    ### 进阶作业
    
    - 设置KV Cache最大占用比例为0.4，开启W4A16量化，以命令行方式与模型对话
    
    ![Untitled](Lecture%205%20LMDeploy%20%E9%87%8F%E5%8C%96%E9%83%A8%E7%BD%B2%20LLM%20%E5%AE%9E%E8%B7%B5%200c567535aa0c44de915de94349da3e82/Untitled%205.png)
    
    ![Untitled](Lecture%205%20LMDeploy%20%E9%87%8F%E5%8C%96%E9%83%A8%E7%BD%B2%20LLM%20%E5%AE%9E%E8%B7%B5%200c567535aa0c44de915de94349da3e82/Untitled%206.png)
    
    - 以API Server方式启动 lmdeploy，开启 W4A16量化，调整KV Cache的占用比例为0.4，分别使用命令行客户端与Gradio网页客户端与模型对话
    
    ![Untitled](Lecture%205%20LMDeploy%20%E9%87%8F%E5%8C%96%E9%83%A8%E7%BD%B2%20LLM%20%E5%AE%9E%E8%B7%B5%200c567535aa0c44de915de94349da3e82/Untitled%207.png)
    
    ![Untitled](Lecture%205%20LMDeploy%20%E9%87%8F%E5%8C%96%E9%83%A8%E7%BD%B2%20LLM%20%E5%AE%9E%E8%B7%B5%200c567535aa0c44de915de94349da3e82/Untitled%208.png)
    
    ![Untitled](Lecture%205%20LMDeploy%20%E9%87%8F%E5%8C%96%E9%83%A8%E7%BD%B2%20LLM%20%E5%AE%9E%E8%B7%B5%200c567535aa0c44de915de94349da3e82/Untitled%209.png)
    
    - 使用W4A16量化，调整KV Cache的占用比例为0.4，使用Python代码集成的方式运行internlm2-chat-1.8b模型。
    
    ![Untitled](Lecture%205%20LMDeploy%20%E9%87%8F%E5%8C%96%E9%83%A8%E7%BD%B2%20LLM%20%E5%AE%9E%E8%B7%B5%200c567535aa0c44de915de94349da3e82/Untitled%2010.png)