# MCP（Model Context Protocol）

> 字节高频原题：「MCP vs Function Calling 的区别」。JD 明确要求 MCP。已填好答案。

## 一、MCP 是什么

- MCP = Anthropic 推出的**开放协议**，统一「大模型调用外部工具 / 获取上下文」的方式。
- ==一句话：MCP 是「AI 时代的 USB-C 接口」——工具封装一次，所有框架都能用==
- 解决的核心问题：以前每个系统的工具格式/参数/权限各搞一套（N×M 爆炸），MCP 统一成 N+M。

## 二、三大核心能力 ⭐

| 类型 | 说明 | 例子 |
|---|---|---|
| **Tools** | 可执行的动作（调用） | 查数据、发消息、create_issue |
| **Resources** | 可读的数据源（只读上下文） | 文档、代码库、用户配置 |
| **Prompts** | 预设提示词模板 | 代码审查模板 |

- ==Tools 是「动作」，Resources 是「数据」，Prompts 是「指令模板」==

## 三、MCP vs Function Calling ⭐⭐（必考原题）

- **Function Calling** = LLM API 的「能力/机制」：模型用结构化格式表达「我想调哪个工具、传什么参」，工具执行要你自己写代码。
- **MCP** = 通信「协议」：Client-Server 协议，工具定义和执行都在 Server，Client 遵循协议即可用。
- 二者不是替代而是**互补**：Function Calling 做「模型决策」，MCP 做「工具标准化」。

| 维度 | Function Calling | MCP |
|---|---|---|
| 层级 | LLM API 层 | 通信协议层 |
| 工具发现 | 静态（开发时定义） | 动态（tools/list 运行时获取） |
| 工具执行 | 客户端代码 | Server 端 |
| 复用 | 每个应用自己实现 | 一个 Server 服务多个应用 |

- ==一句话：Function Calling 是「怎么选工具」，MCP 是「工具怎么标准接入」==

## 四、MCP 架构

- 三个角色：**Host**（AI 应用，如 Cursor/Claude Desktop）、**Client**（住在 Host 里的「翻译官」）、**Server**（暴露工具能力）。
- 传输：本地 stdio、远程 HTTP/SSE（Streamable HTTP 是当前标准），消息用 JSON-RPC 2.0。
- 安全三层：能力声明（Server 声明工具，Agent 不越权）+ 授权（敏感操作人工确认）+ 审计日志。

## 五、前端如何接入 MCP ⭐（前端岗重点）

- 前端在 MCP 体系里的职责（不是「只调 API」）：
  1. **会话状态管理**：多轮消息、tool call 中间态。
  2. **上下文裁剪**：token 限制、滑动窗口、摘要压缩。
  3. **Tool 调用 UI 映射**：展示「正在检索」、渲染工具结果/引用来源，把模型的隐式行为转成用户可感知的 UI。
  4. **流式渲染**：处理 token 流、tool call 中断、多阶段输出。

## 六、MCP 常见坑（加分项）

- **Tool schema 不稳定**：别把 API 直接暴露给 LLM，要严格 JSON Schema + 参数约束。
- **Tool 调用死循环**：LLM 可能无限循环调工具，要 step limit + tool cooldown。
- **Tool latency 高**：用 parallel tools + streaming UI 优化。

## 七、概念关系链路（可直接背）

- ==Skills 决定「怎么想」→ MCP 决定「用什么」→ Function Call 决定「怎么调」==
- 用户需求 → Agent（大脑）→ RAG 查资料 / FunctionCall 调工具 / Skills 执行 → MCP 协议通信。
