# Node.js 事件循环 / Stream / BFF

> 面试高频：事件循环（Node vs 浏览器）、Stream、BFF。结合 Agent 岗：Node 是 AI 应用后端 + 流式转发的基础。

## 一、Node 是什么、前端为什么必须会

- Node = 把 Chrome 的 V8 引擎 + 文件系统/网络/进程等能力包一层，让 JS 能在服务器上跑。
- 前端为什么必须会：
  - ① 前后端一体：Next/Nuxt 全栈框架，一部分代码在 Node 上跑。
  - ② 写 BFF 中间层：为前端定制接口、拼数据、转发。
  - ③ AI 应用后端：接 LLM API、流式转发（Agent 岗重点）。
  - ④ 工程化：vite/webpack/pnpm/eslint 全都跑在 Node 上。
- ==Node 让 JS 从「浏览器脚本」变成「通用服务端语言」==

## 二、模块系统：CJS vs ESM

- CJS（CommonJS）：`require` / `module.exports`，同步加载，Node 老标准。
- ESM（ES Module）：`import` / `export`，静态可分析，浏览器原生支持，现代标准。
- `package.json` 里 `"type": "module"` 决定 `.js` 按哪种解析；`.mjs` 强制 ESM、`.cjs` 强制 CJS。
- ==require(esm)：Node 22.12+ 默认支持 `require()` 同步加载 ESM==（前提：模块图里没有顶层 `await`），Node 24 更稳。面试提这个显得新。

## 三、事件循环 ⭐（必考）

### 浏览器 vs Node
- 浏览器：一个宏任务队列 + 一个微任务队列，每执行一个宏任务就清空微任务。
- Node：libuv 的 **6 个阶段**循环，每个阶段有独立队列。

### Node 6 个阶段（记顺序）
`timers → pending → idle/prepare → poll → check → close`

- **timers**：setTimeout/setInterval 到期的回调。
- **pending callbacks**：上一轮遗留的 I/O 回调（如系统错误）。
- **idle/prepare**：内部用，不用记。
- **poll**：核心阶段，处理 I/O 回调；没活时在这里阻塞等新事件。
- **check**：setImmediate 回调。
- **close callbacks**：close 事件（如 socket 关闭）。

### nextTick 与微任务
- `process.nextTick` 优先级最高，在「两个阶段之间」和「当前操作完成、继续循环前」执行。
- Promise 微任务次之。
- ==真题：nextTick > Promise > setTimeout/setImmediate==
- ==经典坑：setImmediate vs setTimeout 谁先？== 顶层执行时不确定（看 poll 状态，主模块里 setImmediate 常先）；在 I/O 回调里 setImmediate 一定先于 setTimeout（poll 后直接进 check）。

## 四、Stream ⭐

### 为什么需要
- 数据大时不一次性读进内存（读 1GB 文件、AI 逐字输出），而是像水管一块块流。
- ==Stream = 分块处理数据，边读边处理，省内存、能边下边用==

### 四类流
- Readable（可读：fs.createReadStream、HTTP 请求体）
- Writable（可写：fs.createWriteStream、HTTP 响应）
- Duplex（双工：socket，可读可写）
- Transform（转换：读→改→写，如压缩 gzip、加密）

### pipe 与背压（backpressure）
- `readable.pipe(writable)` 把可读流接到可写流，自动处理速度差。
- 背压 = 读得快写得慢时，自动暂停读，防止内存爆。

### 结合 Agent：AI 流式输出
- LLM 逐 token 返回，Node 用 `ReadableStream` + `getReader` 一块块读，再经 SSE 转发给浏览器（串周二的 fetch+ReadableStream）。
- ==AI 聊天「打字机」效果 = 服务端 Stream 流式转发 + 浏览器流式渲染==

## 五、Buffer 与粘包半包（串周二）

- Buffer = 二进制数据块，Stream 里流的就是 Buffer。
- 粘包半包：TCP 是字节流、没有消息边界，多个消息可能粘一起（粘包）或被拆开（半包），要自己定协议分割（定长 / 分隔符 / 长度前缀）。

## 六、进程与多核（简单带过）

- Node 单线程，但用 cluster（多进程）/ worker_threads（多线程）/ pm2 来利用多核。
- ==别踩坑：Node 单线程 ≠ 不能并发==，靠事件循环 + 非阻塞 I/O 实现高并发（单线程能扛大量并发连接）。

## 七、Node 22 / 24 新特性（2025，加分项）

- Node 22.12（LTS）：`require(esm)` 默认开启；内置 `--watch`（改文件自动重启）；内置 test runner 增强。
- Node 24（2025.5）：V8 13.6 带来 `Float16Array`、`using`（显式资源管理）、`RegExp.escape()`、`Error.isError()`；`URLPattern` 全局可用；`--permission` 权限模型转正；Undici 7（内置 fetch 更严、新增 WebSocketStream）。
- ==面试说「Node 也有内置 fetch / 内置 test / --watch / require(esm)」显得新==

## 八、结合 Agent：Node 做 AI 后端（BFF 落点）

- BFF = Backend For Frontend：为前端定制的中间层，前端只调 BFF，BFF 再去调 LLM / 各微服务。
- 为什么用 Node 做 BFF：与前端同语言、Stream 流式转发天然顺手、集中做鉴权/限流/日志、不把 LLM 的 API key 暴露给浏览器。
- 典型链路：`浏览器 → Node BFF（POST /api/chat）→ LLM API → SSE 流回浏览器`。
- （BFF 详解、Next + AI SDK 完整写法见 `next nuxt BFF层 水合.md`）
