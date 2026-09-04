# 实时流式（SSE / WebSocket / fetch）

> 目标：前端 Agent 岗位**最核心**的一块——AI 流式输出、实时对话都靠它。必考「为什么 SSE 不用 WS」。

## 一、HTTP 通信基础（先搞清楚"为什么需要新协议"）

### 1. 请求-响应模型
> HTTP 是「一问一答」：客户端必须先发请求，服务端收到后才回响应，服务端不能主动开口。一次请求对应一次响应，请求 = 请求行 + 请求头 + 请求体。

### 2. 无状态
> HTTP 无状态 = 服务端不记得你上次来过，每次请求独立。所以靠 Cookie / Token（请求头里带）补上「我是谁」，才有登录态。

### 3. 为什么服务端不能主动推消息
> 请求-响应模型决定「必须客户端先问」。服务端有新消息（AI 的回答、股价变动）想主动推，纯 HTTP 做不到——这是 SSE / WebSocket 出现的根本原因：绕过「一问一答」。

### 4. 长轮询 Long Polling
> 客户端发请求，服务端不立刻回、hold 住连接，等有数据再返回；客户端拿到后立刻再发。模拟「推送」，但缺点：连接长时间占用、延迟高、空转浪费，不算真实时。

### 5. HTTP缓存
> 已在 [[网络相关 http nginx]] 写全（强缓存 Cache-Control / 协商缓存 ETag·304），这里不重复。

## 二、WebSocket

### 1. 是什么
> 基于 TCP 的**全双工长连接**协议：一次握手后客户端和服务端可随时互相发消息，双向、实时、低延迟。独立协议（`ws://` / `wss://`），不是 HTTP。

### 2. 握手升级
> 先发一个普通 HTTP 请求，带 `Upgrade: websocket` + `Connection: Upgrade`，服务端回 `101 Switching Protocols`，这条 TCP 连接就从 HTTP 升级成 WebSocket 协议，开始双向传帧。
```js
const ws = new WebSocket('wss://example.com/chat')
ws.onopen = () => ws.send('hi')
ws.onmessage = e => console.log(e.data)
```

### 3. 应用场景
> 需要**双向实时**的：聊天、协同编辑、弹幕、游戏、实时行情/看板。共同点：服务端要主动推、客户端也要随时发。

### 4. 心跳保活与断线重连
> WS 本身没有内置心跳，长连接空闲久了会被中间设备（Nginx/负载均衡/运营商）误断。定时发 ping/pong 保活；断线靠监听 `onclose` 手动重连（见八）。

## 三、SSE（Server-Sent Events）⭐

### 1. 是什么
> 基于 HTTP 的**单向**推送（服务端 → 客户端），客户端用 `EventSource` 订阅。跑在普通 HTTP 上，不换协议，天然复用 HTTP 的一切（鉴权/代理/CDN）。

### 2. 怎么用
> 客户端 `new EventSource('/stream')`；服务端返回 `Content-Type: text/event-stream`，每条 `data: xxx\n\n`（空行分隔）。
```js
const es = new EventSource('/api/stream')
es.onmessage = e => console.log(e.data) // 收到一条
es.onerror = () => {}                  // 出错/断线（会自动重连）
es.close()                             // 主动关
```
> 服务端（Node）：
```js
res.writeHead(200, { 'Content-Type': 'text/event-stream', 'Cache-Control': 'no-cache' })
res.write(`data: ${JSON.stringify({ text: 'hello' })}\n\n`)
```

### 3. 应用场景
> 只需「服务端推、客户端基本不回发」的：AI 流式输出、消息通知、实时进度条、股票/比分。AI 对话就是典型——客户端只发一次问题，之后全等模型吐 token。

### 4. 自动重连
> SSE 内置自动重连 + 断点续传：断线后浏览器自动重试，带 `Last-Event-ID` 告诉服务端「续到哪了」，不用自己写重连（比 WS 省心）。

## 四、SSE vs WebSocket（必考 ⭐⭐⭐）

### 1. 一句话区别
> WS 双向、SSE 单向；WS 是独立协议（TCP 上升级），SSE 就基于 HTTP。

### 2. 为什么 AI 流式用 SSE 不用 WS
> ① AI 对话是「服务端单向推、客户端只发一次请求」，不需要双向；② SSE 基于 HTTP，天然穿透代理/负载均衡/CDN、复用鉴权、部署简单；③ 实现简单，不用自己处理粘包/心跳/重连。==一句话：够用 + 省事。==

### 3. 选型对比表

| 维度 | SSE | WebSocket |
|---|---|---|
| 方向 | 单向（服务端→客户端） | 双向 |
| 协议 | 基于 HTTP | 独立协议（TCP 升级） |
| 断线重连 | 内置自动重连 | 手动（自己写） |
| 二进制 | 只文本 | 支持二进制 |
| 代理/CDN 穿透 | 天然支持 | 需配置，偶有坑 |
| 实现复杂度 | 低 | 高（心跳/粘包/重连） |
| 典型场景 | AI 流式、通知 | 聊天、协同编辑 |

## 五、fetch + ReadableStream 流式读取 ⭐

### 1. 为什么用 fetch 而不是 EventSource
> `EventSource` 只能 GET、不能带自定义 header（传不了 `Authorization`）、只能单向。AI 接口要 POST + 鉴权 + 能中断（AbortController），所以用 `fetch` 拿响应流自己解析。

### 2. 流式读取写法
```js
const res = await fetch(url, { method: 'POST', headers: { Authorization: 'Bearer x' }, body })
const reader = res.body.getReader()
const decoder = new TextDecoder()
while (true) {
  const { done, value } = await reader.read()
  if (done) break
  console.log(decoder.decode(value, { stream: true })) // 逐段打印
}
```

### 3. 与 SSE 的关系
> AI 接口常返回 SSE 格式文本，但用 `fetch` 消费：拿到流后手动按 `\n\n` 切块、解析每块的 `data:` 行。即「用 fetch 消费 SSE 格式」，绕开 EventSource 的 GET/无 header 限制。

## 六、流式 → UI 渲染 ⭐（Agent 岗差异化核心）

### 1. 打字机效果
> 拿到 token 流逐字 append 到 state/DOM。两个坑：自动滚动到底（看最新内容）；别每个 token 都触发全量渲染（节流/批量，或直接改 textContent）。

### 2. 增量 Markdown 渲染
> 流式返回的是**不完整** Markdown，直接整段渲染会闪烁/错乱（代码块 ``` 没闭合、表格没写完）。做法：积累文本再增量渲染（marked / react-markdown），或等段落/代码块完整再渲染那一块。

### 3. React 里怎么消费流
```js
useEffect(() => {
  const ac = new AbortController()
  ;(async () => {
    const res = await fetch(url, { signal: ac.signal })
    const reader = res.body.getReader()
    const dec = new TextDecoder()
    while (true) {
      const { done, value } = await reader.read()
      if (done) break
      setText(t => t + dec.decode(value, { stream: true })) // 追加
    }
  })()
  return () => ac.abort() // 卸载中断 + 清理
}, [])
```
> 或用 Vercel AI SDK 的 `useChat` / `useCompletion` 封装；React 18 Suspense + streaming 是另一条（服务端渲染流）线。

### 4. Tool Call 的 UI 展示（Agent 特有 ⭐）
> Agent 调工具时前端要展示中间态：「正在调用搜索 / 传了什么参数」（loading 卡片、可折叠调用日志），工具结果如何流式拼进最终回答。这是 Agent 岗区别于普通前端的关键——不只看「一句话回答」，还要把「思考 + 工具调用链」可视化。

## 七、取消与超时（AbortController）⭐

### 1. AbortController 取消
```js
const ac = new AbortController()
fetch(url, { signal: ac.signal })
ac.abort() // 「停止生成」按钮就是调这个
```

### 2. 超时处理
> `setTimeout` 到点 abort，或直接 `AbortSignal.timeout(ms)`；abort 后 `reader.read()` 抛 `AbortError`，要 catch 住别冒泡成全局错误。

### 3. 关闭 SSE / WS
> `EventSource.close()` 关 SSE；`WebSocket.close()` 关 WS。组件卸载时清理监听、关闭连接，否则内存泄漏（Agent 长会话尤其注意，见 [[浏览器相关]] GC 节）。

## 八、断线重连与心跳 ⭐

### 1. 心跳保活
> 中间设备（Nginx、负载均衡、运营商 NAT）会把空闲连接断开。心跳 = 定时发小包（WS 的 ping/pong 或自定义消息）证明「我还活着」。

### 2. 重连策略
> 指数退避（1s→2s→4s→上限 30s）+ 随机抖动（防多客户端同时重连打爆服务端）+ 重连上限 + 状态恢复（续传/重发/重新鉴权）。

### 3. SSE 自动重连 vs WS 手动重连
> SSE 浏览器自动重连（带 Last-Event-ID 续传）；WS 要自己监听 `onclose` 手动重连。==这是「AI 流式选 SSE」的又一个理由：省掉一整套重连逻辑。==

## 九、粘包与半包（TCP 层）

### 1. 什么是粘包/半包
> TCP 是字节流、无消息边界。多次 `send` 可能合并成一次收到（**粘包**）；一次 `send` 可能拆成多次收到（**半包**）。收到一串字节，不知道哪到哪是一条消息。

### 2. 为什么 HTTP 没这问题
> HTTP 有明确边界：`Content-Length` 声明 body 长度，或 `chunked` 分块传输，读到指定长度/结束块就知道一条响应完了。所以用 HTTP（含 SSE）不用担心；只有**裸 TCP 自定义长连接**才要自己拆包。

### 3. 怎么解决
> 三种：定长（每条固定长度）、分隔符（`\n` 等）、长度前缀（消息头带 body 长度，先读长度再读 body）。Node 里 `Buffer` 累加 + 按长度切。

## 十、其他（一句话带过）

### 1. WebRTC
> 浏览器间 P2P 传音视频/数据，属「页面间通信」高级场景，见 [[跨端 跨页面通讯]]。

### 2. 桌面/跨端通信
> 已移至 [[跨端 跨页面通讯]]（跨端通信节：JSBridge / Electron IPC / 小程序）。

### 3. WebTransport（前沿）
> 基于 HTTP/3(QUIC) 的双向流式传输，比 WS 更底层、能多路复用，是未来实时通信方向，目前还在普及。
