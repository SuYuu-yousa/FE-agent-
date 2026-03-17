# RAG 全景框架

## RAG 是什么（一句话）

```
用户提问 → 先从知识库里检索相关内容 → 把检索到的内容塞进 prompt → LLM 基于这些内容回答
```

**解决的核心问题：LLM 不知道你的私有数据 / 会编造信息**

## 最基础的 RAG 流程

```
离线阶段（建库）：
  文档 → 切片(Chunking) → 向量化(Embedding) → 存入向量数据库（高维数组）

在线阶段（查询）：
  用户问题 → 向量化 → 从向量库检索相似片段（找到原文字） → 拼进 prompt → 调 LLM → 回答
```

## 完整知识体系框架

按流程顺序，每个环节列出关键主题和热点：

```
RAG 全流程
│
├── 1. 数据处理层
│   ├── 1.1 文档解析（PDF/Word/HTML/表格/图片）
│   ├── 1.2 Chunking 策略 🔥
│   │   ├── 固定长度切片
│   │   ├── 语义切片
│   │   ├── 递归切片
│   │   ├── 按文档结构切片（标题/段落）
│   │   └── Agentic Chunking（用LLM来决定怎么切）
│   ├── 1.3 元数据提取与管理
│   └── 1.4 数据清洗与去重
│
├── 2. 索引层（Indexing）
│   ├── 2.1 Embedding 模型选择 🔥
│   │   ├── OpenAI / Cohere / BGE / Jina
│   │   └── 微调 Embedding 模型
│   ├── 2.2 向量数据库选择
│   │   ├── Chroma / Pinecone / Milvus / Weaviate / FAISS
│   │   └── 各自的适用场景和性能差异
│   ├── 2.3 索引结构 🔥
│   │   ├── 平铺索引（最基础）
│   │   ├── 层级索引（Summary → Detail）
│   │   ├── 知识图谱索引（GraphRAG）🔥🔥
│   │   └── 混合索引
│   └── 2.4 多模态索引（图片/表格/音频）
│
├── 3. 检索层（Retrieval）🔥🔥 研究最多的部分
│   ├── 3.1 基础检索
│   │   ├── 向量相似度检索（语义）
│   │   ├── 关键词检索（BM25）
│   │   └── 混合检索（Hybrid Search）🔥
│   ├── 3.2 查询优化 🔥🔥
│   │   ├── Query Rewriting（让LLM改写用户问题）
│   │   ├── HyDE（让LLM先生成假设答案，用答案去检索）
│   │   ├── Step-back Prompting（先问更宏观的问题）
│   │   ├── Sub-query Decomposition（拆成多个子问题分别检索）
│   │   └── Multi-query（一个问题生成多个变体去检索）
│   ├── 3.3 重排序（Reranking）🔥
│   │   ├── Cross-encoder Reranker
│   │   ├── Cohere Rerank / BGE Reranker
│   │   └── LLM-based Reranking
│   ├── 3.4 检索后处理
│   │   ├── 去重 / 过滤 / 压缩
│   │   └── Context Compression（压缩检索结果，去掉无关部分）
│   └── 3.5 递归/迭代检索
│       ├── 检索→阅读→发现不够→再检索
│       └── Self-RAG 🔥🔥（LLM自己判断要不要检索、检索结果有没有用）
│
├── 4. 生成层（Generation）
│   ├── 4.1 Prompt 设计
│   │   ├── 怎么把检索结果塞进 prompt
│   │   ├── 引用标注（让LLM标出信息来源）
│   │   └── 防幻觉指令
│   ├── 4.2 长上下文处理
│   │   ├── Lost in the Middle 问题 🔥
│   │   ├── 上下文窗口管理
│   │   └── Map-Reduce / Refine 策略
│   └── 4.3 多轮对话中的 RAG
│       ├── 对话历史与检索的结合
│       └── 指代消解
│
├── 5. 评估层（Evaluation）🔥 很重要但经常被忽略
│   ├── 5.1 检索质量评估
│   │   ├── Recall / Precision / MRR / NDCG
│   │   └── 检索到的内容相关吗？
│   ├── 5.2 生成质量评估
│   │   ├── Faithfulness（忠实于检索内容吗？）
│   │   ├── Relevance（回答了用户问题吗？）
│   │   └── Harmfulness（有害信息？）
│   ├── 5.3 评估框架
│   │   ├── RAGAS 🔥
│   │   ├── TruLens
│   │   └── LLM-as-Judge
│   └── 5.4 端到端评估 vs 分模块评估
│
├── 6. 进阶架构 🔥🔥
│   ├── 6.1 Naive RAG → Advanced RAG → Modular RAG 演进
│   ├── 6.2 GraphRAG（微软）🔥🔥🔥
│   │   └── 用知识图谱替代/增强向量检索
│   ├── 6.3 Self-RAG 🔥🔥
│   │   └── 模型自己决定要不要检索、检索结果是否有用
│   ├── 6.4 Corrective RAG (CRAG) 🔥
│   │   └── 检索结果不好时自动纠正策略
│   ├── 6.5 Adaptive RAG 🔥
│   │   └── 根据问题复杂度决定用不用 RAG / 用哪种策略
│   ├── 6.6 Agentic RAG 🔥🔥
│   │   └── 把 RAG 和 Agent 结合，Agent 决定检索策略
│   └── 6.7 Cache-Augmented Generation（最近新方向）
│
└── 7. 工程落地问题
    ├── 7.1 生产环境部署架构
    ├── 7.2 数据更新与增量索引
    ├── 7.3 多租户/权限控制
    ├── 7.4 成本控制（Embedding成本/LLM调用成本）
    ├── 7.5 延迟优化
    └── 7.6 与现有企业系统集成
```

## 当前热度排序（个人判断）

```
非常热 🔥🔥🔥
  ├── GraphRAG
  ├── Agentic RAG
  └── 检索策略优化（Query Rewriting / Hybrid Search）

很热 🔥🔥
  ├── Self-RAG / CRAG / Adaptive RAG
  ├── Reranking
  ├── Chunking 策略研究
  └── 评估体系（RAGAS 等）

稳定重要 🔥
  ├── Embedding 模型选型与微调
  ├── 混合检索
  ├── 多模态 RAG
  └── 长上下文与 RAG 的关系（长上下文窗口会不会让RAG没用？）
```



