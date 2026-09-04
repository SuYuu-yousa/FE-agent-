# zustand（状态管理）

> 目标：现代 React 项目最常用的轻量状态管理。面试常问「为什么用 zustand 不用 Redux」。已填好答案。

## 一、为什么需要状态管理

### Context 的局限
Context 值一变，所有用它的消费者全重渲染；且 Provider 一多就嵌套地狱。跨组件共享状态 + 性能都搞不定，所以需要专门的状态库。

### 什么时候需要
全局状态（用户信息、主题、购物车）、跨多组件共享、避免 props 层层传（prop drilling）。局部状态用 useState 就够了，别啥都塞进全局。

## 二、zustand 基本用法 ⭐

### 创建 store
`create((set) => ({ count: 0, add: () => set(s => ({ count: s.count + 1 })) }))`——一个 store 里放状态 + 改状态的方法。

### 使用
`useStore(s => s.count)` 精确订阅，只在你取的那部分变了才重渲染；不是整个 store 变了就全刷。

### 与 Redux 对比
zustand 无 Provider、无 reducer、无 action type，样板少、心智负担小；Redux 重但可预测、生态全（中间件/devtools）。现在中小项目基本 zustand，大团队要强约束才上 Redux。

## 三、核心特性

### 选择器与浅比较
selector 返回对象要用 `shallow`，避免每次都返回新对象导致无谓重渲染。

### 异步 action
set 里可以直接写 async，不像 Redux 要中间件（thunk/saga）。

### 持久化 / devtools 中间件
`persist` 存 localStorage、`devtools` 接调试。

### 场景
主题、登录态、购物车、AI 对话列表——跨组件又要性能的场景。
