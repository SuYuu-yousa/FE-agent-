## React 学习框架（由浅到深）

---

# 1. 基础入门
先会“用”。

### 必会
- React 是什么，解决什么问题
- 组件化思想
- JSX 语法
- 函数组件
- props
- state
- 事件绑定
- 条件渲染
- 列表渲染
- 表单受控组件
- 父子组件通信
- key 的作用

### 目标
能独立写中小页面。

---

# 2. Hooks 阶段
进入 React 核心开发模式。

### 必会
- `useState`
- `useEffect`
- `useRef`
- `useMemo`
- `useCallback`
- `useContext`

### 重点理解
- 为什么更新 state 会重新渲染
- `useEffect` 依赖数组和清理函数
- 闭包陷阱
- React 渲染和副作用的区别

### 目标
能写业务页面，不乱用 hooks。

---

# 3. 组件设计与复用
从“会写页面”变成“会写组件”。

### 必会
- 组件拆分
- props 设计
- 状态提升
- 受控 / 非受控组件
- 组合优于继承
- 插槽思想（children）
- 自定义 Hooks

### 目标
能写可复用组件，代码结构清晰。

---

# 4. React 工程化配套
实际项目开发必须会。

### 必会
- React Router
- 状态管理（先会 Context，再学 Redux / Zustand）
- 接口请求封装
- 样式方案（CSS Modules / styled-components / Tailwind 了解）
- 表单库了解
- 项目目录设计

### 目标
能搭完整项目。

---

# 5. React 原理
面试核心阶段。

### 必会
- 虚拟 DOM 是什么
- Diff 的基本思想
- key 为什么重要
- React 更新流程
- setState / useState 是异步还是同步
- 批量更新
- 函数组件为什么会重新执行
- hooks 为什么不能放在条件里
- hooks 的链表/顺序思想
- 合成事件
- 生命周期（顺便知道类组件）

### 目标
能解释“为什么这样设计”。

---

# 6. 性能优化
中高级常问。

### 必会
- `React.memo`
- `useMemo`
- `useCallback`
- `useRef`
- 状态下沉 / 状态提升的取舍
- 避免不必要渲染
- 列表优化
- 懒加载
- 代码分割
- 虚拟列表

### 目标
知道哪里会产生性能问题，以及怎么优化。

---

# 7. React 18 与进阶能力
加分项。

### 了解/掌握
- Fiber 是什么，为什么要有 Fiber
- 并发更新基本概念
- `useTransition`
- `useDeferredValue`
- 自动批处理
- Suspense
- Error Boundary
- SSR / CSR / SSG 基本概念
- Next.js 基础

### 目标
具备大厂面试深挖能力。

---

# 8. 配套补强
和 React 一起学。

### 建议同步学
- JavaScript 基础和原理
- TypeScript
- 浏览器事件循环
- HTTP / 网络基础
- 前端工程化
- 组件库思维

---

# 学习顺序建议

### 第一阶段：先会写
- JSX
- 组件
- props/state
- 事件
- 条件/列表渲染

### 第二阶段：会写业务
- hooks
- 生命周期思维
- 组件通信
- 路由
- 请求

### 第三阶段：会搭项目
- 状态管理
- 工程结构
- 封装组件
- TS

### 第四阶段：会讲原理
- 虚拟 DOM
- Diff
- hooks 原理
- Fiber
- 性能优化

---

# 面试优先级
如果你目标是校招/大厂：

### 最重要
- JSX / 组件 / props / state
- `useState` / `useEffect`
- 组件通信
- key
- React Router
- 状态管理
- 虚拟 DOM / Diff
- hooks 规则
- 性能优化

### 其次
- Fiber
- React 18
- Suspense
- SSR / Next.js

---

