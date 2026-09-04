# AI SDK 与流式后端

> 前端 Agent 岗核心：LLM 前端生态最常用的库，Vercel AI SDK 是事实标准。已给大纲，自己填。

## 一、AI SDK 是什么
> 要写：AI SDK 是 Vercel 出的统一接入大模型的 SDK；核心 `streamText`（后端流式生成）+ `useChat`（前端 hook）；一套协议打通前后端。

## 二、streamText + useChat 怎么配合
> 要写：后端 streamText 返回 ReadableStream，前端 useChat 自动解析；一个完整流式聊天的最小实现（后端 Route Handler + 前端组件）。

## 三、Data Stream Protocol ⭐
> 要写：AI SDK 的前后端通信协议，SSE 格式（`data:` + JSON）；每帧有 type：`text-delta`（正文）、`reasoning-*`（思考）、`tool-*`（工具调用）、`start-step`/`finish-step`（步骤）、`finish`（结束）。

## 四、为什么聊天用 SSE 不用 WebSocket ⭐（必考）
> 要写：单向上行够了；SSE 走 HTTP 简单、能复用负载均衡/鉴权；EventSource 自动重连；WS 双向但复杂。三句话答清。

## 五、完整链路
> 要写：浏览器 → BFF(Node/Next) → LLM API → SSE 流回前端，一张图讲清每一段。

## 六、token 合并渲染
> 要写：流式 token 到前端后怎么高效渲染（rAF 合并 delta、局部更新，串「AI 内容渲染」那篇）。

## 七、LangChain.js 概览
> 要写：LangChain.js 是什么（Agent 编排框架）、和 AI SDK 的分工（LangChain 管 Agent 逻辑、AI SDK 管流式接入）、什么场景用哪个。
