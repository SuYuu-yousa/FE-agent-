

## 一、文档结构与语义化

### 1. DOCTYPE 与标准/怪异模式

==`<!DOCTYPE html>` 告诉浏览器用「标准模式」渲染；不写会进「怪异模式(quirks)」，盒模型/布局按老规则走、表现不一致。写它就是为了统一到 HTML5 标准。==

怪异渲染模式和怪异盒模型不一样，前者没人用了，后者经常用

### 2. 语义化标签> 
article / section / nav / aside / header / footer / main这些东西，提到会聊SEO和可读性

==SEO = 搜索引擎优化，让页面在百度/Google 里排名更靠前、更容易被收录。==

==TDK、结构化数据、语义化标签都是 SEO 手段。语义化面试一句话带过：用对标签(header/nav/main)即可。==

TDK—Title（标题）、Description（描述）、Keywords（关键词）

语义化为什么能促进SEO？
搜索引擎靠爬虫读 HTML，只能靠标签猜哪个是重点。用了 语义标签，爬虫就能准确知道：标题是啥、正文在哪、导航在哪 


### 3. 行内元素 vs 块级元素
> 要写：块级（div/p/h1/ul）独占一行可设宽高；行内（span/a/strong）不换行、宽高由内容决定；行内块 `inline-block`。

块级默认单独一行，两个就会换行
行级写并列的两个会在一起

div  p是最经典的块级

span a 是最经典的行内

inline-block是什么，有什么具体场景要用它，比如？

==inline-block 兼两者：像行内一样不换行、能并排，又能设宽高和上下 margin。场景：导航 li 排一行、图文并排、做小标签/徽章。==

### 4. src vs href
> 要写：`src` 用于替换当前元素（script/img/iframe），会阻塞解析；`href` 用于建立引用关系（link/a），可并行加载。

==src(script/img/iframe) 是「替换」当前元素，浏览器读到就停下下载执行、阻塞解析；href(link/a) 只「建立引用」，可并行下载、不阻塞。==



## 二、meta 标签

### 1. viewport
> 要写：移动端适配基础，`width=device-width, initial-scale=1.0`；不加会怎样（手机按 980px 渲染、页面缩小）。

经常用，但不知道原理

==原理：手机默认按 980px 宽度渲染网页再整体缩小，字小到看不清。viewport 让浏览器按设备真实宽度渲染：width=device-width 布局宽=设备宽，initial-scale=1 初始缩放 1 倍。不写就缩成一团。==
一般用法：

```
<meta name="viewport" content="width=device-width, initial-scale=1.0>
```

```
各属性含义：
┌──────────────────┬───────────────────────────────┐
│ width            │ 视口宽度，device-width=设备宽度  │
│ initial-scale    │ 初始缩放比例（1.0=不缩放）       │
│ maximum-scale    │ 最大缩放比例                    │
│ minimum-scale    │ 最小缩放比例                    │
│ user-scalable    │ 是否允许用户手动缩放（no/yes）    │
└──────────────────┴───────────────────────────────┘
//核心是width=divice-width （window.innerWidth 会随 viewport 设置而变化）设定了window元素的款是设备宽
```

写完这俩，效果就是正常占据上半段手机屏幕

### 2. charset
==charset 声明文档字符编码为 UTF-8，防止中文乱码，必须放 head 最前。==


### 3. http-equiv
> 要写：模拟 HTTP 响应头，如 `refresh`、`X-UA-Compatible`、`Content-Security-Policy`。

==http-equiv 用来「模拟 HTTP 响应头」，如 refresh 定时刷新/跳转、或下发 CSP。现代用得少，知道能干嘛即可。==

为什么少用了——它模拟的这些头，现在都有更靠谱的地方做：

```
<meta http-equiv="Content-Security-Policy" 
  content="default-src 'self'; script-src 'self' https://cdn.example.com; 
           style-src 'self' 'unsafe-inline'; img-src *; 
           connect-src 'self' https://api.example.com">
```

| 旧用法 | 为什么少用 | 现在换成 |
| --- | --- | --- |
| refresh 定时刷新/跳转 | 体验差、易被钓鱼劫持 | JS `setTimeout` + `location.href` |
| X-UA-Compatible | 只服务旧 IE | IE 淘汰，没用了 |
| Content-Security-Policy | meta 里写有解析延迟、部分指令(如 frame-ancestors)不生效 | 真实 HTTP 响应头 |

==核心：http-equiv 是在 HTML 里「假扮」响应头，安全头放服务器真响应头更快、更全，所以只剩兼容场景。==

==XSS→CSP 这条线：XSS 是注入恶意脚本 → CSP 是白名单(只允许列出的来源执行脚本)防它 → 白名单放服务器响应头比 http-equiv 模拟更早生效、能力更全。==

### 4. CSP
> 要写：Content-Security-Policy 是什么，能防 XSS，常见指令 `default-src` / `script-src`。

XSS是注入攻击，有好几种，一种是把攻击语句写到发给后端的内容里，后端会执行直接把用户资料返现到前端页面上？

==CSP(Content-Security-Policy)：声明页面允许加载哪些来源的脚本/样式/图片，从源头拦截注入的恶意脚本，是防 XSS 主力。指令：default-src、script-src、img-src。==

==XSS 分三种：==

==① 存储型——脚本存进后端(如评论里写<script>偷cookie</script>，网站没过滤就渲染了 /<script/>)，别人打开页面就执行偷cookie脚本，最危险；==

==② 反射型——脚本藏在 URL 参数里被服务器原样回显；
攻击者造一个链接：博客.com/搜索?关键词=<偷cookie的脚本>
受害者点击链接，页面会显示「你搜索的是：<偷cookie的脚本>」，执行脚本==

==③ DOM 型——前端 innerHTML 插不可信数据触发。本质都是脚本在受害者浏览器里执行、偷 cookie/冒充用户。==

==SQL 注入：用户输入里塞 SQL(如 ' OR '1'='1)，后端拼字符串被当 SQL 执行导致拖库。防御：参数化查询/ORM 预编译，绝不拼接 SQL。==


![[Pasted image 20260831200652.png]]


## 三、script 标签


### 1. 默认vs async vs defer

![[Pasted image 20260831213507.png]]

默认 /<script/> 解析到就暂停、下载执行完再继续(阻塞)；(基本不用了)

async 异步下载、下载完立刻执行、不保证顺序(适合第三方脚本)；独立的，要尽早开始跑的

defer 异步下载、等 DOM 解析完按声明顺序执行(最常用，业务 JS)。业务代码要操作dom，所以有顺序，按声明顺序

所以对于一个大项目里面的所有js文件，他们运行的谁先谁后，本质是defer的声明顺序决定的，只不过打包工具帮我们做了这个事情

bundle = 打包工具把你几十上百个模块（文件）按 import 依赖合并成的一个 JS 文件。
### 2. 加载与渲染阻塞
> 要写：JS 阻塞 DOM 解析；CSS 阻塞渲染但不阻塞 DOM 解析；为什么把 script 放 body 底部。

这块详细说说

==① JS 阻塞 DOM 解析——脚本可能 document.write 改 DOM，浏览器遇到 script 就停下执行。② CSS 阻塞渲染但不阻塞 DOM 解析——没 CSS 画不出页面，但 DOM 可以先建。③ 所以 JS 放 body 底部(或 defer)、CSS 放 head。补充：JS 执行前要等它之前的 CSS 加载完。==

### 实际用法

```html
<!-- 业务 JS：defer 放 head，等 DOM 解析完按声明顺序执行（最常用） -->
<script src="app.js" defer></script>

<!-- 第三方独立脚本：async，下载完立刻执行，不依赖 DOM / 顺序 -->
<script src="analytics.js" async></script>

<!-- 默认阻塞：一般放 body 底部兜底，能不用就不用 -->
<script src="legacy.js"></script>
```

==选型：脚本互相依赖、要操作 DOM → defer；完全独立(统计/广告) → async；不加就是阻塞，尽量不用。==

==补充：动态 `document.createElement('script')` 插入的脚本，默认就是 async 行为。==

## 四、HTML5 新特性

### 1. Web Storage
> 要写：localStorage（永久）/ sessionStorage（会话级）；容量、同源策略、与 cookie 区别（周二详讲）。

这些分别是平时适用于什么场景的来着？帮我补充一下

我记得的：

cookie

indexDB存比较大的数据

session ST是关掉了这次浏览器窗口就没了（你可以多写点这种区分细节，但不要太多）

==cookie 约4KB、每次请求自动带 header(存登录态，可加 httpOnly/secure)；==

==localStorage 5-10MB、永久、不随请求(存用户偏好/token/草稿)；==

==sessionStorage 容量同 localStorage、关标签页就没了(单次会话临时态)；==

==IndexedDB 几百MB、异步存对象/二进制(大文件/离线数据)。==

==一句话：cookie 跟着请求走，local/sessionStorage 只在前端，IndexedDB 是"前端数据库"。==

对于agent&前端可能用到的，一般用什么存？


### 2. History API
==History API 是「不刷新页面就改地址栏」：pushState 加历史、replaceState 替换、popstate 监听前进后退，SPA 路由靠它。==

==区分：跨窗口通信是 postMessage，那是另一回事。==

React Router 里你 navigate('/settings')，它底层就是：
history.pushState → 改地址栏 + 换组件渲染



### 3. Web Worker / Service Worker
> 要写：Web Worker 做多线程计算（不能操作 DOM）；Service Worker 做离线缓存/PWA、拦截请求、独立于页面生命周期。

线程和进程的区别是什么？

==进程是资源分配单位(独立内存)，线程是进程内执行单位(共享内存)，一个进程至少一个主线程。JS 跑主线程，Web Worker 是独立线程、postMessage 通信、不能碰 DOM，用来挪走耗时计算。==

进程可以调度，线程可以并行，这个说法对吗？

### 4. WebSocket / SSE
> 要写：一句话区别即可（双向 vs 单向推送），**周二专门深挖**。

websockket和webRTC这种都是双向吗，就是建立一个不断的链接通道不用每次都发头尾之类的，用于音视频传输？

SSE是什么我忘了

单纯的请求是每次都建立链接通道的，

==普通 HTTP 每次「建连→请求→响应→断开」，服务端不能主动推；==

==WebSocket 一次握手后长连接、双向发消息(聊天/协同/弹幕)；==

==SSE 基于 HTTP 单向推流(服务端→客户端，适合 AI 流式输出)；==

==WebRTC 是浏览器间 P2P 传音视频/数据，和 WebSocket 不同(一个传媒体、一个传消息)。==

### 5. Canvas / SVG
> 要写：Canvas 是位图、靠 JS 绘制；SVG 是矢量图、基于 XML、可绑定事件；适用场景。

==Canvas 位图、JS 画像素、画完是一张图不能单独绑事件(游戏/图表/图片处理)；
SVG 矢量、放大不糊、每个元素可绑事件可 CSS 改(图标/地图)。==

Canvas（像素、画完就忘）
- 游戏（王者荣耀画面）
- 图表库底层（ECharts 折线/柱状）
- 图片处理/滤镜（美图、截图编辑）
- 地图轨迹、热力图

SVG（矢量、能选中能缩放）
- 图标、Logo（淘宝、GitHub 图标）
- 地图（高德地图缩放不糊）
- 需要点击/悬停交互的图形（饼图点某块高亮）
- 简单动画、流程图

记法：内容复杂、量大、不点它 → Canvas；简单、要放大、要交互 → SVG。

### 6. Web Components
> 要写：Custom Elements / Shadow DOM / Template / Slot 各自作用；隔离样式与 DOM。

==Web Components 原生自定义组件：Custom Elements(自定义标签+生命周期)、Shadow DOM(样式/DOM 隔离)、Template(不渲染的模板)、Slot(插槽)。跨框架复用，但生态和 DX 不如 React/Vue 成熟。==

插槽 = React 的 children

## 五、DOM 与 BOM

### 1. DOM 树构建
> 要写：HTML → DOM 树的过程；与 CSSOM、渲染树的关系（周二详讲）。

==URL→页面：查缓存→DNS 解析(域名→IP)→TCP 连接(CDN 就近节点)→发请求→返 HTML 建 DOM 树→CSS 建 CSSOM→合成渲染树→布局→绘制。不同资源(HTML/CSS/JS/图片)可能来自不同域名(CDN/第三方/主站)，各自单独请求、单独缓存，所以有 preload/懒加载/域名收敛等优化。你说的"最近节点"就是 CDN 边缘节点。==

把preload/懒加载/域名收敛这些优化放到后续计划里讲解

### 2. DOM API


==DOM API 就是 querySelector/getElementById 这套原生操作，框架里用得少(React 用 ref/虚拟 DOM)。==

==减少重排：DocumentFragment 批量插、批量改 class 或一次 cssText、动画用 transform/opacity、读写分离别在循环里边读边改、用 requestAnimationFrame 合并。性能优化 md 已列进计划。==





### 3. 三个 Observer
> 要写：`MutationObserver`（监听 DOM 变化）、`IntersectionObserver`（监听元素进入视口，做懒加载/曝光）、`ResizeObserver`（监听尺寸变化）。

这个很重要，你可以详细说说里面有哪些常用API以及使用场景是怎样的

==三个都是「异步监听变化」的 API，比轮询省性能。==

==① IntersectionObserver 监听元素进视口：图片懒加载、无限滚动、曝光埋点(核心 API：observe(el)、回调里 isIntersecting、threshold)。==

==② MutationObserver 监听 DOM 子节点/属性变化：富文本同步、水印防篡改。==

==③ ResizeObserver 监听任意元素尺寸变化(不像 window.resize 只看窗口)：图表/容器自适应重绘。==

懒加载例子
<!-- 图片不写 src，真实地址先藏在 data-src -->
<img data-src="真实图.jpg" />

// 1. new 一个观察器，塞回调：图片进视口时干嘛
const observer = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {      // 进视口了！
      const img = entry.target       // 就是那张图
      img.src = img.dataset.src      // 把真实地址填上，开始加载
      observer.unobserve(img)        // 加载完，不用再盯它了
    }
  })
})

// 2. 让它盯住每张懒加载的图
document.querySelectorAll('img[data-src]').forEach(img => {
  observer.observe(img)   // 盯！
})


时刻1：new IntersectionObserver(回调)
       → 造观察器，回调只是"存起来"，没执行

时刻2：observe(img)
       → 向浏览器登记"盯着这张图，变了叫我"
       → 这一步不跑回调，纯登记

时刻3：用户在滚动页面
       → 浏览器自己在跟踪 img 位置（内部机制，你不管）

时刻4：img 滚进视口了（状态变了）
       → 浏览器调你的回调，把结果塞进 entries

时刻5：回调跑一次
       → if (isIntersecting) 为 true → 填 src
       → 执行完，回调结束，不会一直跑

MutationObserver（监听 DOM 变化）

// 1. new 观察器，塞回调
const observer = new MutationObserver((mutations) => {
  mutations.forEach(m => {
    console.log('DOM 变了', m.type)   // 'childList' 子节点增删 / 'attributes' 属性变
  })
})

// 2. observe 登记，多一个配置：监听什么
observer.observe(el, {
  childList: true,   // 监听子节点增删
  attributes: true,  // 监听属性变化
})

ResizeObserver（监听元素尺寸）

// 1. new 观察器，塞回调
const observer = new ResizeObserver((entries) => {
  entries.forEach(e => {
    console.log(e.contentRect.width, e.contentRect.height)  // 新宽高
  })
})

// 2. observe 登记，和 Intersection 几乎一模一样
observer.observe(el)

### 参数与回调值

| Observer | observe 参数 | 什么时候回调 | 回调里可用值 |
| --- | --- | --- | --- |
| Intersection | `observe(el, {threshold, root, rootMargin})` | 进/出视口、相交比例变 | `isIntersecting`、`intersectionRatio`、`target` |
| Mutation | `observe(el, {childList, attributes, characterData, subtree})` | 子节点增删 / 属性变 / 文本变 | `type`、`target`、`addedNodes/removedNodes`、`attributeName` |
| Resize | `observe(el)`（不用配置） | 元素 content box 尺寸变 | `contentRect.width/height`、`target` |

==补充：Mutation 是「批量回调」——一批 DOM 变化攒一起、在微任务里统一回调一次，不是变一次调一次。==

### 4. BOM：BOM是啥？没听过，补充
> 要写：`window / location / navigator / history / screen` 各自作用。

==BOM = 浏览器对象模型：window(全局)、location(URL/跳转)、navigator(浏览器/设备信息)、history(前进后退/SPA 路由)、screen(屏幕)。一句话：DOM 管页面内容，BOM 管浏览器本身。==

 其实BOM就是指的除了DOM的那些浏览器对象，你写了html，然后浏览器本来就是把HTML变成一个DOM树，然后浏览器给了你页面里面的一堆对象和他自己的一些对象，页面上那些就是DOM，他自己的就是BOM

![[Pasted image 20260901164351.png]]
写 document.querySelector（改页面）、location.href（让浏览器跳转）——都是 JS 在操作这两个"浏览器塞给你的对象"，就这事。


## 六、新元素与性能属性（2024–2026）

### 1. dialog 元素与 Popover API
> 要写：原生 `<dialog>` + `showModal()` 做模态框；Popover API（`popover` 属性）做原生弹出层，替代手写定位库。

==<dialog/>.showModal() 原生模态框(自带遮罩/焦点锁定/Esc 关闭)；Popover API 给元素加 popover 属性做原生弹出层定位、点外部关闭。==

### 2. loading="lazy" 与 fetchpriority
> 要写：图片/iframe 懒加载 `loading="lazy"`；`fetchpriority="high/low"` 提示加载优先级。

image的一个属性
==loading="lazy" 图片/iframe 懒加载、滚到视口才加载省首屏流量；fetchpriority="high/low" 提示资源加载优先级。==


还有什么节约客户端首屏优化的方法？首屏问题可以作为专题放在后面，记录到你的计划


### 3. prefetch / preload
> 要写：`<link rel="preload">`（本页即将用到、高优先级）；`<link rel="prefetch">`（下个页面可能用到、低优先级）；区别和工程场景。



==preload 预加载本页马上要用的资源(字体/首屏 JS，高优先级)；prefetch 空闲预加载下个页面可能用的(低优先级)。区别：preload 服务本页、prefetch 服务未来页面。==




### 4. inert 与 <search>
> 要写：`inert` 让元素不可交互不可聚焦（做弹窗焦点管理）；`<search>` 语义化搜索区域。

==inert 让元素不可交互/不可聚焦(弹窗打开时给背景加 inert 做焦点管理)；<search> 语义化"搜索区域"标签。==

## 七、SEO

### 1. TDK
> 要写：title / description / keywords 的作用和写法。

==TDK = Title 标题、Description 描述、Keywords 关键词(现在几乎不影响排名)。写在 head，决定搜索结果展示的标题简介。==

### 2. 结构化数据 JSON-LD
> 要写：给搜索引擎机器可读的结构化信息。

==JSON-LD 用 <script type="application/ld+json"> 标出页面机器可读信息(商品价格/评分/作者)，帮搜索引擎识别，可能拿富媒体结果(星标/卡片)。==

### 3. Open Graph
> 要写：`og:title / og:image / og:url` 控制分享链接卡片。

忘记是什么了，一两句话说明

==Open Graph 是社交平台(微信/FB/Twitter)分享链接时读取的 meta(og:title/og:description/og:image)，决定分享卡片显示什么标题、简介、缩略图。==

## 八、无障碍 a11y

### 1. ARIA / role / tabindex
> 要写：ARIA 属性补充语义；`role` 定义角色；`tabindex` 控制键盘导航顺序；为什么重要。

==ARIA 属性(aria-label/aria-hidden)给元素补语义让屏幕阅读器能读；role 定义角色(role="button")；tabindex 控制键盘 Tab 顺序(0 可聚焦、-1 不可 Tab 但可脚本聚焦)。一句话：让残障/键盘用户也能用。==
