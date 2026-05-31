结合自己的学习经历以及几个项目中采样的内容，友链放在文档最后。

```
offerroadLLM/
│
├── README.md                   # 项目主页：进度概览、复习计划、置顶高频考点
├── .gitignore                  # 忽略文件配置
│
├── 📂 00-Math-DL-Basics        # 【基石】数学与深度学习基础
│   ├── Backpropagation.md      # 反向传播推导
│   ├── Normalization.md        # BN/LN/RMSNorm 对比与原理
│   └── ...
│
├── 📂 01-Transformer-Core      # 【核心】Transformer 组件细节
│   ├── Attention-Mechanism.md  # MHA, MQA, GQA 区别与计算量分析
│   ├── Positional-Encoding.md  # RoPE, ALiBi, 绝对/相对位置编码
│   └── ...
│
├── 📂 02-LLM-Architecture      # 【模型】主流模型架构与演进
│   ├── Llama-Series.md         # Llama 1/2/3 架构演进与区别
│   ├── MoE-Architecture.md     # Mixtral/DeepSeek MoE 原理与负载均衡
│   └── Tokenizer.md            # BPE, WordPiece 词表构建原理
│   
├── 📂 03-Training-Tuning       # 【训练】预训练与微调 (SFT/RLHF)
│   ├── PEFT-LoRA.md            # LoRA/QLoRA 原理与代码实现细节
│   ├── RLHF-DPO.md             # PPO 流程与 DPO 优缺点对比
│   └── Distributed-Training.md # ZeRO, Pipeline Parallelism, Tensor Parallelism
│
├── 📂 04-RAG-Embedding         # 【应用】检索增强生成
│   ├── Embedding-Model.md      # 双塔模型、对比学习、MTEB榜单
│   ├── RAG-Optimization.md     # 召回策略、Rerank、向量数据库选型
│   └── Vector-Database.md      # HNSW, IVF 索引原理
│
├── 📂 05-Inference-Deploy      # 【工程】推理加速与部署
│   ├── KV-Cache.md             # 原理与显存占用估算
│   ├── Quantization.md         # AWQ, GPTQ, INT8/FP4 量化原理
│   └── vLLM-PagedAttention.md  # 显存管理优化技术
│
├── 📂 06-Hand-Torn-Code        # 【手撕】高频手写代码题
│   ├── impl_attention.py       # 手写 Multi-Head Attention
│   ├── impl_nms.py             # 手写 NMS
│   └── leetcode-hot-100.md     # 算法刷题笔记
│
├── 📂 99-Interview-Records     # 【实战】真实面试复盘
│   ├── 202401-CompanyA-Algorithm.md
│   ├── 202402-CompanyB-LLM-Resarch.md
│   └── Template.md             # 面试复盘通用模版
│
└── 📂 img                     # 存放所有 Markdown引用的图片
        ├── transformer-arch.png
        └── rope-vis.png
```


## 友链项目

[EasyOffer](https://github.com/jingtian11/EasyOffer)
