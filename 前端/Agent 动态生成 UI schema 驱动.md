# Agent 动态生成 UI（schema 驱动渲染）

> JD 要求 1：前端 Agent 岗 vs 普通前端岗的最大差异点，本质是给 Agent 用的「UI SDK」。已给大纲，自己填。

## 一、为什么需要动态生成 UI
> 要写：Agent 通过 tool call 返回结构化数据，前端要把「机器生成的结果」动态渲染成界面；不能写死 HTML。

## 二、schema 结构设计 ⭐
> 要写：`{ type, props, children }` 组件描述；一个完整 schema 例子（button/card/table）。

## 三、组件注册表 registry ⭐
> 要写：`{ button: Button, card: Card, table: Table }` 查表渲染；递归渲染 children（组件树）。

## 四、异步组件加载
> 要写：组件多了要懒加载（动态 import），首屏别全量注册。

## 五、白名单兜底 + 安全 ⭐
> 要写：未知 type 用默认组件/白名单过滤，防止 Agent 输出恶意或非法结构；数据与 UI 解耦。

## 六、配合 tool call 渲染
> 要写：AI SDK 的 tool-call 帧拿到结果 → 转成 schema → 渲染成工具卡片（串「Agent 过程可视化」）。

## 七、代码示例
> 要写：一个最小可跑的 schema 驱动渲染（registry + 递归 + 兜底）。
