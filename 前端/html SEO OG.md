# HTML

## 🎯 校招复习建议

```

HTML
├── 语义化标签（article/section/nav/aside/header/footer）
├── meta 标签作用（viewport/charset/http-equiv/CSP）
├── HTML5 新特性
│   ├── Web Components（Shadow DOM/Custom Elements/Template/Slot）
│   ├── Canvas / SVG 基础
│   ├── Drag & Drop API
│   ├── Web Storage（localStorage/sessionStorage）
│   ├── History API（pushState/replaceState）
│   ├── Geolocation / Web Worker / Service Worker
│   └── WebSocket / Server-Sent Events
├── SEO 基础（TDK/结构化数据/Open Graph）
├── 无障碍（ARIA 属性 / role / tabindex）
└── DOM 与 BOM
    ├── DOM 树构建过程
    ├── DOM API（增删改查/遍历/片段DocumentFragment）
    ├── MutationObserver
    ├── IntersectionObserver
    ├── ResizeObserver
    └── BOM（window/location/navigator/history/screen）
重要程度排序：

🔴 必须掌握：语义化、行内/块级、async/defer、meta viewport、本地存储区别
🟡 建议掌握：DOCTYPE、src/href、HTML5 新特性、Canvas/SVG
🟢 了解即可：iframe、Web Components、无障碍（ARIA）
```

> **实话说**：纯 HTML 题在面试中占比不大（大概 5-10%），但如果答不上来会**很减分**，因为面试官会觉得基础不扎实。建议花 **1-2天** 快速过一遍就够了，重点精力放在 **JS、CSS、框架、计算机网络、手写题** 上。

------

## 一、语义化标签

常见语义化标签：

| 标签                        | 用途             |
| --------------------------- | ---------------- |
| `<header>`                  | 页头/区块头部    |
| `<nav>`                     | 导航             |
| `<main>`                    | 主要内容（唯一） |
| ==`<article>`==             | ==独立内容块==   |
| `<section>`                 | 内容分区         |
| `<aside>`                   | 侧边栏           |
| `<footer>`                  | 页脚             |
| `<figure>` / `<figcaption>` | 图片+说明        |
| `<time>`                    | 时间             |
| `<mark>`                    | 高亮文本         |



## 二、meta 标签

### meta :**提供关于 HTML 文档的元数据**

### viewport（移动端适配基础）

```
<meta name="viewport" content="width=device-width, initial-scale=1.0, 
  maximum-scale=1.0, user-scalable=no">
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

### charset（字符编码）

```
<meta charset="UTF-8">
```

### ==http-equiv（模拟 HTTP 响应头）==

**配不了后端/服务器响应头时，用 `meta http-equiv` 在前端页面里兜底声明一些规则**。但能力和生效时机都不如真正响应头。

```
<!-- 页面自动刷新/跳转 -->
<meta http-equiv="refresh" content="5;url=https://example.com">

<!-- 设置文档的 MIME 类型和编码（旧写法，charset 替代） -->
<meta http-equiv="Content-Type" content="text/html;charset=UTF-8">

<!-- IE 兼容模式：使用最新渲染引擎 -->
<meta http-equiv="X-UA-Compatible" content="IE=edge">

<!-- 缓存控制（实际项目中更推荐用真正的 HTTP 响应头） -->
<meta http-equiv="Cache-Control" content="no-cache">
<meta http-equiv="Pragma" content="no-cache">
```

### ==CSP（Content-Security-Policy）⭐⭐==

```
<meta http-equiv="Content-Security-Policy" 
  content="default-src 'self'; script-src 'self' https://cdn.example.com; 
           style-src 'self' 'unsafe-inline'; img-src *; 
           connect-src 'self' https://api.example.com">
```

```
CSP 是什么：内容安全策略，防止 XSS 攻击的重要手段

核心指令：
┌─────────────┬─────────────────────────────────┐
│ default-src  │ 默认策略，其他指令的回退值         │
│ script-src   │ 允许加载 JS 的来源               │
│ style-src    │ 允许加载 CSS 的来源              │
│ img-src      │ 允许加载图片的来源               │
│ connect-src  │ 允许 AJAX/WebSocket 连接的地址    │
│ font-src     │ 允许加载字体的来源               │
│ frame-src    │ 允许嵌入 iframe 的来源           │
│ media-src    │ 允许加载音视频的来源              │
└─────────────┴─────────────────────────────────┘

值：
'self'          → 只允许同源
'none'          → 完全禁止
'unsafe-inline' → 允许内联（一般不推荐）
'unsafe-eval'   → 允许 eval（一般不推荐）
https://xxx.com → 指定域名

实际中更推荐用 HTTP 响应头设置 CSP（而非 meta 标签）
```

### 其他常用 meta

```
<!-- SEO 相关 -->
<meta name="description" content="网页描述，显示在搜索结果中">
<meta name="keywords" content="关键词1,关键词2">（现已几乎无用）
<meta name="robots" content="index,follow">  <!-- 搜索引擎索引策略 -->

<!-- 社交分享（Open Graph 在后面 SEO 部分细讲） -->
<meta property="og:title" content="分享标题">
<meta property="og:image" content="分享图片URL">

<!-- 移动端相关 -->
<meta name="theme-color" content="#ffffff">  <!-- 浏览器地址栏颜色 -->
<meta name="format-detection" content="telephone=no"> <!-- 禁止自动识别电话 -->
<meta name="apple-mobile-web-app-capable" content="yes"> <!-- iOS 全屏模式 -->
```

------

## 三、HTML5 新特性

### 3.1 Web Components ⭐

Web Components 是浏览器原生的**组件化方案**，不依赖任何框架。

#### Custom Elements（自定义元素）

```
// 定义自定义元素
class MyButton extends HTMLElement {
  // 当元素被插入 DOM 时调用
  connectedCallback() {
    this.innerHTML = `<button class="my-btn">${this.getAttribute('label')}</button>`;
    this.querySelector('button').addEventListener('click', () => {
      this.dispatchEvent(new CustomEvent('my-click', {
        detail: { label: this.getAttribute('label') },
        bubbles: true  // 允许事件冒泡
      }));
    });
  }

  // 监听哪些属性的变化
  static get observedAttributes() {
    return ['label', 'disabled'];
  }

  // 当被监听的属性变化时调用
  attributeChangedCallback(name, oldVal, newVal) {
    if (name === 'label' && this.querySelector('button')) {
      this.querySelector('button').textContent = newVal;
    }
  }

  // 元素从 DOM 中移除时调用
  disconnectedCallback() {
    console.log('元素被移除');
  }
}

// 注册自定义元素（名字必须包含连字符）
customElements.define('my-button', MyButton);
```

HTML



```
<!-- 使用自定义元素 -->
<my-button label="点击我"></my-button>
```

text



```
生命周期：
constructor()           → 创建时（不要在这里访问 DOM）
connectedCallback()     → 插入 DOM 时（初始化逻辑放这里）
disconnectedCallback()  → 从 DOM 移除时（清理逻辑）
attributeChangedCallback() → 属性变化时
adoptedCallback()       → 被移动到新文档时（少用）
```

#### Shadow DOM（影子 DOM）

JavaScript



```
class MyCard extends HTMLElement {
  constructor() {
    super();
    // 创建 Shadow DOM（'open' 允许外部通过 JS 访问，'closed' 则不允许）
    const shadow = this.attachShadow({ mode: 'open' });

    shadow.innerHTML = `
      <style>
        /* 这里的样式完全隔离，不会影响外部，外部也不会影响这里 */
        .card {
          border: 1px solid #eee;
          border-radius: 8px;
          padding: 16px;
        }
        /* :host 选择器选中宿主元素本身 */
        :host {
          display: block;
          margin: 10px;
        }
        /* :host() 带条件的选择 */
        :host([theme="dark"]) .card {
          background: #333;
          color: white;
        }
        /* ::slotted() 选择被分发到 slot 的元素 */
        ::slotted(h2) {
          color: blue;
        }
      </style>
      <div class="card">
        <slot name="title"></slot>   <!-- 具名插槽 -->
        <slot></slot>               <!-- 默认插槽 -->
        <slot name="footer"></slot>  <!-- 具名插槽 -->
      </div>
    `;
  }
}

customElements.define('my-card', MyCard);
```

HTML



```
<my-card theme="dark">
  <h2 slot="title">卡片标题</h2>
  <p>这是默认插槽内容</p>
  <span slot="footer">底部信息</span>
</my-card>
```

text



```
Shadow DOM 核心特性：
1. 样式隔离 → 内部样式不泄露，外部样式不入侵
2. DOM 隔离 → document.querySelector 找不到 Shadow DOM 内的元素
3. 事件隔离 → 部分事件不会穿过 Shadow 边界

与 Vue scoped / CSS Modules 的区别：
- Vue scoped：通过 data 属性 + 选择器权重实现，本质还是全局 CSS
- CSS Modules：编译时生成唯一类名
- Shadow DOM：浏览器原生隔离，真正的边界
```

#### Template & Slot（模板与插槽）

HTML



```
<!-- template 标签：内容不会被渲染，作为模板使用 -->
<template id="user-template">
  <div class="user">
    <img class="avatar">
    <span class="name"></span>
  </div>
</template>

<script>
  const template = document.getElementById('user-template');
  // 克隆模板内容
  const clone = template.content.cloneNode(true);
  clone.querySelector('.name').textContent = '张三';
  clone.querySelector('.avatar').src = 'avatar.jpg';
  document.body.appendChild(clone);
</script>
```

text



```
三大技术的关系：
Custom Elements → 定义新标签（行为）
Shadow DOM      → 封装内部结构和样式（隔离）
Template/Slot   → 定义可复用的模板和内容分发（结构）

三者结合 = 完整的 Web Components 方案
```

------

### 3.2 Canvas / SVG 基础

#### Canvas（位图，像素级操作）

JavaScript



```
const canvas = document.getElementById('myCanvas');
const ctx = canvas.getContext('2d');  // 获取 2D 渲染上下文

// 基本图形绑制
ctx.fillStyle = '#ff0000';
ctx.fillRect(10, 10, 100, 50);        // 填充矩形
ctx.strokeStyle = '#0000ff';
ctx.strokeRect(10, 10, 100, 50);       // 描边矩形

// 路径绘制
ctx.beginPath();
ctx.moveTo(50, 50);                    // 移动到起点
ctx.lineTo(200, 50);                   // 画线到终点
ctx.lineTo(125, 150);                  // 继续画线
ctx.closePath();                       // 闭合路径
ctx.fill();                            // 填充
// ctx.stroke();                       // 或描边

// 圆弧
ctx.beginPath();
ctx.arc(200, 200, 50, 0, Math.PI * 2); // 圆心(200,200) 半径50 完整圆
ctx.fill();

// 文本
ctx.font = '24px Arial';
ctx.fillText('Hello Canvas', 100, 100);

// 图片
const img = new Image();
img.onload = () => ctx.drawImage(img, 0, 0, 200, 150);
img.src = 'photo.jpg';
```

#### SVG（矢量图，XML 描述）

HTML



```
<svg width="400" height="300" xmlns="http://www.w3.org/2000/svg">
  <!-- 矩形 -->
  <rect x="10" y="10" width="100" height="50" fill="red" stroke="blue" />
  
  <!-- 圆 -->
  <circle cx="200" cy="100" r="50" fill="green" />
  
  <!-- 线 -->
  <line x1="10" y1="200" x2="300" y2="200" stroke="black" stroke-width="2" />
  
  <!-- 路径（最灵活） -->
  <path d="M 10 80 Q 95 10 180 80" stroke="blue" fill="none" />
  
  <!-- 文本 -->
  <text x="100" y="250" font-size="20" fill="purple">Hello SVG</text>
  
  <!-- SVG 支持事件绑定！ -->
  <circle cx="300" cy="200" r="30" fill="orange" 
          onclick="alert('clicked!')" style="cursor: pointer" />
</svg>
```

text



```
Canvas vs SVG 对比：
┌───────────┬────────────────────┬────────────────────┐
│           │ Canvas             │ SVG                │
├───────────┼────────────────────┼────────────────────┤
│ 类型       │ 位图（像素）        │ 矢量（数学描述）    │
│ 缩放       │ 会模糊             │ 不失真             │
│ 元素数量   │ 大量元素性能好       │ 元素多了会卡       │
│ 事件       │ 不能绑定到图形上     │ 每个元素都能绑事件  │
│ 操作方式   │ JS 命令式绘制       │ DOM + CSS 声明式   │
│ SEO       │ 不友好             │ 可被搜索引擎读取    │
│ 适用场景   │ 游戏/粒子/大数据    │ 图标/图表/动画     │
│ 动画       │ requestAnimationFrame │ CSS/SMIL 动画    │
└───────────┴────────────────────┴────────────────────┘
```

------

### 3.3 Drag & Drop API

HTML



```
<div id="dragItem" draggable="true">拖我</div>
<div id="dropZone">放到这里</div>
```

JavaScript



```
const dragItem = document.getElementById('dragItem');
const dropZone = document.getElementById('dropZone');

// ===== 拖拽源的事件 =====
dragItem.addEventListener('dragstart', (e) => {
  // 拖拽开始：设置传输数据
  e.dataTransfer.setData('text/plain', e.target.id);
  e.dataTransfer.effectAllowed = 'move';  // 允许的效果
  e.target.style.opacity = '0.5';
});

dragItem.addEventListener('dragend', (e) => {
  // 拖拽结束：恢复样式
  e.target.style.opacity = '1';
});

// ===== 放置目标的事件 =====
dropZone.addEventListener('dragover', (e) => {
  e.preventDefault();  // 必须阻止默认行为才能触发 drop！
  e.dataTransfer.dropEffect = 'move';
});

dropZone.addEventListener('dragenter', (e) => {
  e.preventDefault();
  dropZone.classList.add('drag-over');  // 拖入时添加高亮
});

dropZone.addEventListener('dragleave', () => {
  dropZone.classList.remove('drag-over');  // 拖出时移除高亮
});

dropZone.addEventListener('drop', (e) => {
  e.preventDefault();
  const id = e.dataTransfer.getData('text/plain');
  const element = document.getElementById(id);
  dropZone.appendChild(element);  // 移动元素
  dropZone.classList.remove('drag-over');
});
```

text



```
事件触发顺序：
dragstart → drag（持续触发）→ dragenter → dragover（持续触发）→ drop → dragend

关键注意点：
1. 必须设置 draggable="true"
2. dragover 必须 preventDefault() 才能触发 drop
3. dataTransfer 是拖拽事件的数据传输对象

文件拖拽：
dropZone.addEventListener('drop', (e) => {
  const files = e.dataTransfer.files;  // 获取拖入的文件
  for (const file of files) {
    console.log(file.name, file.size, file.type);
  }
});
```

------

### 3.4 Web Storage

JavaScript



```
// ========== localStorage ==========
// 持久化存储，关闭浏览器后仍然存在
// 同源共享，大小约 5MB

localStorage.setItem('user', JSON.stringify({ name: '张三', age: 25 }));
const user = JSON.parse(localStorage.getItem('user'));
localStorage.removeItem('user');
localStorage.clear();  // 清空所有
console.log(localStorage.length);  // 存储的键值对数量
console.log(localStorage.key(0));  // 获取第 n 个 key

// ========== sessionStorage ==========
// 会话级存储，关闭标签页就清除
// 同源 + 同标签页（新开标签页不共享！）
sessionStorage.setItem('token', 'abc123');

// ========== 监听存储变化（跨标签页通信！） ==========
window.addEventListener('storage', (e) => {
  console.log('变化的 key:', e.key);
  console.log('旧值:', e.oldValue);
  console.log('新值:', e.newValue);
  console.log('来源:', e.url);
  console.log('存储区域:', e.storageArea);
});
// 注意：storage 事件只在「其他」同源标签页触发，当前页面不触发
```

text



```
对比：
┌─────────────────┬─────────────────┬─────────────────┬────────────┐
│                 │ localStorage    │ sessionStorage  │ Cookie     │
├─────────────────┼─────────────────┼─────────────────┼────────────┤
│ 生命周期         │ 永久（手动清除）  │ 标签页关闭即清除  │ 设置过期时间 │
│ 大小            │ ~5MB            │ ~5MB            │ ~4KB       │
│ 随请求发送       │ 否              │ 否              │ 是         │
│ 同源共享         │ 是              │ 否（仅同标签页）  │ 是         │
│ API             │ getItem/setItem │ getItem/setItem │ document.cookie │
│ 跨标签页通信     │ storage 事件     │ 不支持           │ 不支持      │
└─────────────────┴─────────────────┴─────────────────┴────────────┘

使用场景：
localStorage  → 用户偏好设置、主题、缓存数据
sessionStorage → 表单临时数据、页面状态、一次性登录信息
Cookie → 身份认证（httpOnly）、服务端需要读取的信息
```

------

### 3.5 History API

JavaScript



```
// ========== pushState（添加新的历史记录） ==========
// 不会刷新页面！只是改变 URL 和历史栈
history.pushState(
  { page: 'about' },     // state 对象（可存任意可序列化数据）
  '',                      // title（大多数浏览器忽略）
  '/about'                 // 新的 URL（必须同源）
);

// ========== replaceState（替换当前历史记录） ==========
// 用新记录替换当前记录，不增加历史栈
history.replaceState(
  { page: 'home' },
  '',
  '/home'
);

// ========== popstate 事件 ==========
// 用户点击浏览器 前进/后退 按钮时触发
// 注意：pushState/replaceState 不会触发此事件！
window.addEventListener('popstate', (e) => {
  console.log('用户导航了');
  console.log('state:', e.state);  // 就是 pushState 时传入的 state 对象
  // 根据 state 或 location 渲染对应页面
  renderPage(e.state);
});

// ========== 其他 History 方法 ==========
history.back();       // 后退
history.forward();    // 前进
history.go(-2);       // 后退两步
history.go(1);        // 前进一步
console.log(history.length);  // 历史栈的长度
console.log(history.state);   // 当前的 state
```

text



```
前端路由的两种模式：

Hash 模式：
URL: https://example.com/#/about
监听: window.addEventListener('hashchange', handler)
特点: # 后面的内容不会发送给服务器，兼容性好

History 模式：
URL: https://example.com/about
API: pushState + popstate
特点: URL 更美观，但需要服务端配合（所有路径返回 index.html）

// 简单的前端路由实现
class Router {
  constructor() {
    this.routes = {};
    window.addEventListener('popstate', () => this.handle());
  }
  
  register(path, callback) {
    this.routes[path] = callback;
  }
  
  push(path) {
    history.pushState(null, '', path);
    this.handle();
  }
  
  handle() {
    const path = location.pathname;
    const handler = this.routes[path];
    handler && handler();
  }
}
```

------

### 3.6 Geolocation / Web Worker / Service Worker

#### Geolocation（地理定位）

JavaScript



```
// 获取一次位置
navigator.geolocation.getCurrentPosition(
  (position) => {
    console.log('纬度:', position.coords.latitude);
    console.log('经度:', position.coords.longitude);
    console.log('精度:', position.coords.accuracy, '米');
    console.log('海拔:', position.coords.altitude);
    console.log('速度:', position.coords.speed);
  },
  (error) => {
    switch (error.code) {
      case error.PERMISSION_DENIED: console.log('用户拒绝'); break;
      case error.POSITION_UNAVAILABLE: console.log('位置不可用'); break;
      case error.TIMEOUT: console.log('超时'); break;
    }
  },
  {
    enableHighAccuracy: true,  // 高精度模式（GPS）
    timeout: 5000,             // 超时时间
    maximumAge: 0              // 不使用缓存位置
  }
);

// 持续监听位置变化
const watchId = navigator.geolocation.watchPosition(successCb, errorCb);
navigator.geolocation.clearWatch(watchId);  // 停止监听
```

#### Web Worker（多线程）⭐

JavaScript



```
// ===== 主线程（main.js）=====
const worker = new Worker('worker.js');

// 发送消息给 Worker
worker.postMessage({ type: 'calculate', data: [1, 2, 3, 4, 5] });

// 接收 Worker 的消息
worker.onmessage = (e) => {
  console.log('计算结果:', e.data);
};

// 错误处理
worker.onerror = (e) => {
  console.error('Worker 错误:', e.message);
};

// 终止 Worker
worker.terminate();

// ===== Worker 线程（worker.js）=====
self.onmessage = (e) => {
  const { type, data } = e.data;
  
  if (type === 'calculate') {
    // 模拟耗时计算（不会阻塞主线程 UI！）
    let sum = 0;
    for (let i = 0; i < 1000000000; i++) {
      sum += i;
    }
    
    // 返回结果给主线程
    self.postMessage(sum);
  }
};

// Worker 中不能访问：DOM、window、document、localStorage
// Worker 中可以访问：navigator、location、XMLHttpRequest、fetch、IndexedDB
```

text



```
Worker 的种类：
┌──────────────────┬──────────────────────────────────────┐
│ Dedicated Worker │ 专用 Worker，只能被创建它的页面使用      │
│ Shared Worker    │ 共享 Worker，多个同源页面可共用          │
│ Service Worker   │ 特殊的 Worker，代理网络请求，支持离线    │
└──────────────────┴──────────────────────────────────────┘

使用场景：
- 大量数据计算（排序、加密、图像处理）
- 复杂数据解析（CSV、Excel）
- 实时数据处理
- 避免主线程卡顿
```

#### Service Worker（离线缓存、PWA 核心）⭐

JavaScript



```
// ===== 注册 Service Worker（主线程）=====
if ('serviceWorker' in navigator) {
  navigator.serviceWorker.register('/sw.js', {
    scope: '/'  // 控制的范围
  }).then(reg => {
    console.log('SW 注册成功，scope:', reg.scope);
  }).catch(err => {
    console.error('SW 注册失败:', err);
  });
}

// ===== Service Worker 文件（sw.js）=====

const CACHE_NAME = 'my-cache-v1';
const urlsToCache = ['/', '/styles.css', '/app.js', '/logo.png'];

// install 事件：安装时预缓存资源
self.addEventListener('install', (event) => {
  event.waitUntil(
    caches.open(CACHE_NAME).then(cache => {
      console.log('预缓存资源');
      return cache.addAll(urlsToCache);
    })
  );
  self.skipWaiting();  // 跳过等待，立即激活
});

// activate 事件：激活时清理旧缓存
self.addEventListener('activate', (event) => {
  event.waitUntil(
    caches.keys().then(names => {
      return Promise.all(
        names.filter(name => name !== CACHE_NAME)
             .map(name => caches.delete(name))
      );
    })
  );
  self.clients.claim();  // 立即控制所有页面
});

// fetch 事件：拦截网络请求 ⭐
self.addEventListener('fetch', (event) => {
  event.respondWith(
    // 缓存优先策略
    caches.match(event.request).then(cached => {
      if (cached) return cached;  // 有缓存就用缓存
      
      return fetch(event.request).then(response => {
        // 缓存新请求的资源
        const clone = response.clone();
        caches.open(CACHE_NAME).then(cache => {
          cache.put(event.request, clone);
        });
        return response;
      });
    })
  );
});
```

text



```
Service Worker 生命周期：
注册 → 下载 → 安装(install) → 等待 → 激活(activate) → 控制页面(fetch)

缓存策略：
1. Cache First   → 缓存优先（适合静态资源）
2. Network First → 网络优先（适合 API 数据）
3. Stale While Revalidate → 先用缓存，后台更新
4. Network Only  → 仅网络
5. Cache Only    → 仅缓存

特点：
- 必须 HTTPS（或 localhost）
- 不能访问 DOM
- 可以拦截和代理所有网络请求
- 独立于页面生命周期（页面关闭后仍可运行）
- 支持推送通知（Push API）
- 支持后台同步（Background Sync）
```

------

### 3.7 WebSocket / Server-Sent Events

#### WebSocket（全双工通信）⭐

JavaScript



```
// 创建连接
const ws = new WebSocket('wss://example.com/chat');

// 连接打开
ws.addEventListener('open', () => {
  console.log('连接已建立');
  ws.send('Hello Server');           // 发送文本
  ws.send(JSON.stringify({ type: 'join', room: '123' }));  // 发送 JSON
  ws.send(new ArrayBuffer(8));       // 发送二进制
});

// 接收消息
ws.addEventListener('message', (event) => {
  const data = JSON.parse(event.data);
  console.log('收到:', data);
});

// 连接关闭
ws.addEventListener('close', (event) => {
  console.log('关闭码:', event.code);
  console.log('关闭原因:', event.reason);
  console.log('是否正常关闭:', event.wasClean);
  // 重连逻辑
  setTimeout(() => reconnect(), 3000);
});

// 错误处理
ws.addEventListener('error', (event) => {
  console.error('WebSocket 错误');
});

// 主动关闭
ws.close(1000, '正常关闭');

// 检查状态
console.log(ws.readyState);
// 0: CONNECTING  1: OPEN  2: CLOSING  3: CLOSED

// ========== 带心跳的封装 ==========
class WS {
  constructor(url) {
    this.url = url;
    this.heartbeatInterval = null;
    this.reconnectAttempts = 0;
    this.maxReconnect = 5;
    this.connect();
  }
  
  connect() {
    this.ws = new WebSocket(this.url);
    
    this.ws.onopen = () => {
      this.reconnectAttempts = 0;
      this.startHeartbeat();
    };
    
    this.ws.onclose = () => {
      this.stopHeartbeat();
      if (this.reconnectAttempts < this.maxReconnect) {
        const delay = Math.min(1000 * 2 ** this.reconnectAttempts, 30000);
        setTimeout(() => {
          this.reconnectAttempts++;
          this.connect();
        }, delay);  // 指数退避重连
      }
    };
  }
  
  startHeartbeat() {
    this.heartbeatInterval = setInterval(() => {
      if (this.ws.readyState === WebSocket.OPEN) {
        this.ws.send(JSON.stringify({ type: 'ping' }));
      }
    }, 30000);  // 每 30 秒发送心跳
  }
  
  stopHeartbeat() {
    clearInterval(this.heartbeatInterval);
  }
}
```

#### Server-Sent Events（SSE，服务端推送）

JavaScript



```
// SSE：服务端 → 客户端 的单向推送
const eventSource = new EventSource('/api/stream');

// 接收消息
eventSource.addEventListener('message', (e) => {
  console.log('收到:', e.data);
});

// 自定义事件类型
eventSource.addEventListener('notification', (e) => {
  const data = JSON.parse(e.data);
  showNotification(data);
});

// 连接打开
eventSource.addEventListener('open', () => {
  console.log('SSE 连接已建立');
});

// 错误处理（SSE 会自动重连！）
eventSource.addEventListener('error', (e) => {
  if (e.target.readyState === EventSource.CLOSED) {
    console.log('连接关闭');
  }
});

// 关闭连接
eventSource.close();
```

text



```
WebSocket vs SSE 对比：
┌─────────────┬──────────────────────┬──────────────────────┐
│             │ WebSocket            │ SSE                  │
├─────────────┼──────────────────────┼──────────────────────┤
│ 通信方向     │ 双向（全双工）        │ 单向（服务端→客户端） │
│ 协议         │ ws:// / wss://       │ 基于 HTTP            │
│ 数据格式     │ 文本 + 二进制         │ 仅文本               │
│ 自动重连     │ 需手动实现            │ 浏览器自动重连        │
│ 跨域         │ 支持                 │ 支持（CORS）         │
│ 浏览器支持   │ 很好                 │ 除 IE 外都支持       │
│ 适用场景     │ 聊天/游戏/协同编辑    │ 通知推送/股票行情/    │
│             │                      │ AI 流式输出          │
└─────────────┴──────────────────────┴──────────────────────┘

SSE 在 AI 场景中非常重要：
ChatGPT 等 LLM 的流式输出就是用 SSE 实现的
```

------

## 四、SEO 基础

### TDK（Title / Description / Keywords）

HTML



```
<!-- Title：最重要的 SEO 标签 -->
<!-- 建议：核心关键词 - 品牌名，控制在 60 字符内 -->
<title>前端面试题大全 - 2024最新整理 | TechBlog</title>

<!-- Description：搜索结果中的摘要 -->
<!-- 建议：包含关键词，150-160 字符，有吸引力 -->
<meta name="description" content="全面整理2024年前端面试高频考点，
涵盖HTML、CSS、JavaScript、React、Vue等核心知识点及详解。">

<!-- Keywords：现在搜索引擎基本忽略，但写上无害 -->
<meta name="keywords" content="前端面试,JavaScript,React,面试题">
```

### 结构化数据（JSON-LD）

HTML



```
<!-- 告诉搜索引擎页面内容的结构化信息 -->
<!-- Google 会用这些数据生成富媒体搜索结果（Rich Snippets） -->
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Article",
  "headline": "前端面试题大全",
  "author": {
    "@type": "Person",
    "name": "张三"
  },
  "datePublished": "2024-01-15",
  "dateModified": "2024-06-01",
  "image": "https://example.com/cover.jpg",
  "publisher": {
    "@type": "Organization",
    "name": "TechBlog",
    "logo": {
      "@type": "ImageObject",
      "url": "https://example.com/logo.png"
    }
  },
  "description": "全面整理前端面试高频考点..."
}
</script>

<!-- 其他常用类型 -->
<!-- @type: "Product"       → 商品（价格、评分、库存） -->
<!-- @type: "BreadcrumbList" → 面包屑导航 -->
<!-- @type: "FAQ"           → 常见问题 -->
<!-- @type: "HowTo"         → 教程步骤 -->
<!-- @type: "Recipe"        → 食谱 -->
```

### Open Graph（社交分享）

HTML



```
<!-- Facebook / 微信 / 钉钉等平台分享时展示的卡片信息 -->
<meta property="og:title" content="前端面试题大全">
<meta property="og:description" content="2024年最新前端面试考点整理">
<meta property="og:image" content="https://example.com/share.jpg">
<meta property="og:url" content="https://example.com/article/1">
<meta property="og:type" content="article">  <!-- website/article/product -->
<meta property="og:site_name" content="TechBlog">

<!-- Twitter Card（Twitter 分享） -->
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:title" content="前端面试题大全">
<meta name="twitter:description" content="...">
<meta name="twitter:image" content="https://example.com/share.jpg">
```

### 其他 SEO 实践

HTML



```
<!-- 语义化标签（前面已讲） -->
<!-- 正确的标题层级 h1 → h2 → h3 -->
<!-- 图片 alt 属性 -->
<img src="photo.jpg" alt="2024前端技术趋势图表">

<!-- canonical 避免重复内容 -->
<link rel="canonical" href="https://example.com/article/1">

<!-- robots 控制爬虫 -->
<meta name="robots" content="index,follow">     <!-- 允许索引和跟踪链接 -->
<meta name="robots" content="noindex,nofollow">  <!-- 禁止索引和跟踪 -->

<!-- sitemap（网站地图） -->
<!-- 放在 robots.txt 中或提交给搜索引擎 -->
```

------

## 五、无障碍（Accessibility / a11y）

### ARIA 属性

HTML



```
<!-- ARIA = Accessible Rich Internet Applications -->
<!-- 为屏幕阅读器等辅助技术提供额外语义信息 -->

<!-- role：定义元素的角色 -->
<div role="navigation">导航区域</div>
<div role="main">主内容</div>
<div role="alert">重要提示信息</div>
<div role="dialog" aria-modal="true">对话框</div>
<div role="button">自定义按钮</div>
<ul role="tablist">
  <li role="tab" aria-selected="true">选项卡1</li>
  <li role="tab" aria-selected="false">选项卡2</li>
</ul>

<!-- aria-label：给元素添加文本标签（无可见文字时） -->
<button aria-label="关闭菜单">
  <svg>...</svg>  <!-- 图标按钮没有文字，需要 aria-label -->
</button>

<!-- aria-labelledby：引用另一个元素作为标签 -->
<h2 id="section-title">用户设置</h2>
<form aria-labelledby="section-title">...</form>

<!-- aria-describedby：引用描述元素 -->
<input type="password" aria-describedby="pwd-hint">
<span id="pwd-hint">密码至少8位，包含数字和字母</span>

<!-- aria-hidden：对辅助技术隐藏 -->
<span aria-hidden="true">🎨</span>  <!-- 纯装饰性内容 -->

<!-- aria-live：动态内容区域 -->
<div aria-live="polite">  <!-- 内容变化时，等当前朗读完再播报 -->
  搜索结果：找到 15 条
</div>
<div aria-live="assertive">  <!-- 内容变化时，立即中断播报 -->
  错误：密码不正确
</div>

<!-- aria-expanded：展开/折叠状态 -->
<button aria-expanded="false" aria-controls="menu">菜单</button>
<ul id="menu" hidden>...</ul>

<!-- aria-disabled / aria-required / aria-invalid -->
<input aria-required="true" aria-invalid="true">
```

### role（角色）

HTML



```
<!-- 常用 role 值 -->
<!--
  role="banner"        → 网站级头部（通常是 header）
  role="navigation"    → 导航区域
  role="main"          → 主内容区
  role="complementary" → 辅助内容（类似 aside）
  role="contentinfo"   → 网站级底部（通常是 footer）
  role="search"        → 搜索区域
  role="form"          → 表单区域
  role="alert"         → 警告信息
  role="status"        → 状态信息
  role="dialog"        → 对话框
  role="tooltip"       → 提示框
  role="progressbar"   → 进度条
  role="tablist/tab/tabpanel" → 选项卡
  role="menu/menuitem" → 菜单
  role="tree/treeitem" → 树形控件
-->

<!-- 原则：优先使用原生 HTML 语义标签，只有自定义组件才需要 role -->
<!-- 好 --> <button>提交</button>
<!-- 差 --> <div role="button" tabindex="0">提交</div>
```

### tabindex（键盘导航）

HTML



```
<!-- tabindex 控制元素是否可聚焦以及 Tab 键的导航顺序 -->

<!-- tabindex="0"：将非交互元素加入 Tab 顺序（按 DOM 顺序） -->
<div tabindex="0" role="button" onclick="handleClick()">
  自定义可聚焦元素
</div>

<!-- tabindex="-1"：可以通过 JS focus() 聚焦，但不在 Tab 序列中 -->
<div tabindex="-1" id="error-message">
  这里有错误  <!-- JS 可以 element.focus() 让屏幕阅读器读到 -->
</div>

<!-- tabindex="1+"：指定 Tab 顺序（不推荐！打乱自然顺序） -->
<input tabindex="2">  <!-- 不要这样做 -->
<input tabindex="1">  <!-- 不要这样做 -->
```

text



```
键盘可访问性清单：
✅ 所有交互元素可通过 Tab 聚焦
✅ 焦点可见（不要 outline: none 除非有替代样式）
✅ Enter/Space 可触发按钮
✅ Esc 可关闭弹窗
✅ 弹窗中焦点被锁定（Focus Trap）
✅ 关闭弹窗后焦点回到触发元素

// Focus Trap 示例（弹窗内焦点循环）
dialog.addEventListener('keydown', (e) => {
  if (e.key !== 'Tab') return;
  const focusable = dialog.querySelectorAll('button, input, [tabindex="0"]');
  const first = focusable[0];
  const last = focusable[focusable.length - 1];
  
  if (e.shiftKey && document.activeElement === first) {
    e.preventDefault();
    last.focus();  // Shift+Tab 从第一个到最后一个
  } else if (!e.shiftKey && document.activeElement === last) {
    e.preventDefault();
    first.focus();  // Tab 从最后一个到第一个
  }
});
```

------

## 六、DOM 与 BOM

### 6.1 DOM 树构建过程 ⭐⭐

text



```
浏览器从拿到 HTML 到渲染出页面的完整流程：

1. 解析 HTML → 构建 DOM 树
   字节(Bytes) → 字符(Characters) → 令牌(Tokens) → 节点(Nodes) → DOM 树
   
   <html>
   ├── <head>
   │   ├── <meta>
   │   ├── <title>
   │   └── <link>
   └── <body>
       ├── <div>
       │   ├── <h1>
       │   └── <p>
       └── <script>

2. 解析 CSS → 构建 CSSOM 树
   CSS 文件/style 标签 → Token 化 → CSSOM 树
   （每个节点携带计算后的样式信息）

3. DOM + CSSOM → 合成 Render Tree（渲染树）
   - 只包含可见元素（display:none 的不在）
   - 每个节点都有计算好的样式

4. Layout（布局/回流/重排）
   - 计算每个节点的几何信息（位置、大小）
   - 从根节点递归计算

5. Paint（绘制）
   - 将渲染树的各节点绘制到屏幕像素
   - 文字、颜色、边框、阴影、图片等

6. Composite（合成）
   - 将不同层合并显示
   - GPU 加速的层可以独立合成

关键阻塞行为：
┌──────────────┬─────────────────────────────────────────┐
│ CSS          │ 阻塞渲染（不阻塞 DOM 解析）               │
│              │ → CSSOM 没构建好，不会渲染                 │
│ JS（普通）    │ 阻塞 DOM 解析 + 阻塞渲染                  │
│              │ → 遇到 <script>，停下来下载执行完才继续解析 │
│ JS（async）  │ 异步下载，下载完立即执行（可能打断解析）    │
│ JS（defer）  │ 异步下载，DOM 解析完后、DOMContentLoaded   │
│              │   前按顺序执行                            │
└──────────────┴─────────────────────────────────────────┘
```

HTML



```
<!-- script 加载策略 -->
<script src="app.js"></script>            <!-- 阻塞：停止解析 → 下载 → 执行 → 继续 -->
<script src="app.js" async></script>      <!-- 异步下载，下载完就执行（不保证顺序） -->
<script src="app.js" defer></script>      <!-- 异步下载，解析完 DOM 后按序执行 -->

<!-- 最佳实践 -->
<!-- 独立脚本（统计、广告）用 async -->
<!-- 依赖 DOM 或有顺序要求的用 defer -->
<!-- 或者把 script 放到 body 末尾 -->
```

text



```
重排 vs 重绘：
重排（Reflow/Layout）：元素的几何属性变化 → 重新计算布局
  触发：宽高/位置/display/字体大小/窗口大小/添加删除DOM
  代价：非常高，整个渲染流程走一遍

重绘（Repaint）：外观变化但不影响布局 → 跳过 Layout 直接 Paint
  触发：颜色/背景/visibility/box-shadow
  代价：相对较低

优化手段：
1. 批量修改 DOM（DocumentFragment / 一次性 innerHTML）
2. 离线操作（display:none → 修改 → 显示）
3. 避免逐条修改样式，用 class 切换
4. 使用 transform/opacity 做动画（触发 Composite，跳过 Layout+Paint）
5. 读写分离（避免读一次写一次交替，导致强制同步布局）
```

------

### 6.2 DOM API（增删改查/遍历/DocumentFragment）

#### 查询

JavaScript



```
// ===== 查询元素 =====
document.getElementById('app');                    // 返回单个元素
document.getElementsByClassName('item');            // 返回 HTMLCollection（动态）
document.getElementsByTagName('div');               // 返回 HTMLCollection（动态）
document.querySelector('.item');                    // 返回第一个匹配的元素
document.querySelectorAll('.item');                 // 返回 NodeList（静态）

// HTMLCollection vs NodeList
// HTMLCollection：动态集合，DOM 变化会实时反映
// NodeList (querySelectorAll)：静态快照，DOM 变化不影响
// NodeList (childNodes)：动态集合

const items = document.getElementsByClassName('item');
// 如果新增了 class="item" 的元素，items.length 会自动增加

const items2 = document.querySelectorAll('.item');
// 新增元素不会影响 items2
```

#### 创建与增删

JavaScript



```
// ===== 创建元素 =====
const div = document.createElement('div');
const text = document.createTextNode('Hello');
const fragment = document.createDocumentFragment(); // 文档片段

// ===== 插入 =====
parent.appendChild(child);                     // 末尾插入
parent.insertBefore(newNode, referenceNode);   // 在参考节点前插入
parent.append(node1, node2, 'text');           // 末尾插入（支持多个+字符串）
parent.prepend(node);                          // 开头插入
target.before(node);                           // 目标元素前面
target.after(node);                            // 目标元素后面

// ===== 替换 =====
parent.replaceChild(newChild, oldChild);       // 旧 API
oldNode.replaceWith(newNode);                  // 新 API

// ===== 删除 =====
parent.removeChild(child);                     // 旧 API
element.remove();                              // 新 API（直接删除自己）

// ===== 克隆 =====
const clone = element.cloneNode(true);         // true: 深拷贝（含子节点）
                                               // false: 浅拷贝（仅自身）
```

#### 属性操作

JavaScript



```
// ===== 属性 =====
element.getAttribute('data-id');
element.setAttribute('data-id', '123');
element.removeAttribute('data-id');
element.hasAttribute('data-id');

// dataset（data-* 属性的便捷访问）
// <div data-user-id="123" data-name="张三">
element.dataset.userId;   // '123'（自动驼峰转换）
element.dataset.name;     // '张三'

// class 操作
element.classList.add('active');
element.classList.remove('active');
element.classList.toggle('active');          // 有则删，无则加
element.classList.contains('active');        // 是否包含
element.classList.replace('old', 'new');     // 替换

// style 操作
element.style.color = 'red';
element.style.cssText = 'color: red; font-size: 16px;';  // 批量设置
getComputedStyle(element).color;  // 获取计算后的样式（只读）
```

#### 遍历

JavaScript



```
// ===== DOM 遍历 =====
element.parentNode;          // 父节点
element.parentElement;       // 父元素（区别：parentNode 可能是 Document）
element.children;            // 子元素集合（HTMLCollection，不含文本节点）
element.childNodes;          // 子节点集合（NodeList，含文本/注释节点）
element.firstChild;          // 第一个子节点（可能是文本节点 #text）
element.firstElementChild;   // 第一个子元素
element.lastElementChild;    // 最后一个子元素
element.nextSibling;         // 下一个兄弟节点（可能是文本）
element.nextElementSibling;  // 下一个兄弟元素
element.previousElementSibling; // 上一个兄弟元素

// closest() — 向上查找最近的匹配祖先（含自身）
element.closest('.container');  // 非常实用！

// contains() — 是否包含某个后代
parent.contains(child);  // true/false

// matches() — 是否匹配选择器
element.matches('.active');  // true/false
```

#### DocumentFragment（文档片段）⭐

JavaScript



```
// 问题：循环插入 DOM 会触发多次重排
// 差的做法：
for (let i = 0; i < 1000; i++) {
  const li = document.createElement('li');
  li.textContent = `Item ${i}`;
  ul.appendChild(li);  // 每次都触发重排！
}

// 好的做法：使用 DocumentFragment
const fragment = document.createDocumentFragment();
for (let i = 0; i < 1000; i++) {
  const li = document.createElement('li');
  li.textContent = `Item ${i}`;
  fragment.appendChild(li);  // 只在内存中操作，不触发重排
}
ul.appendChild(fragment);  // 一次性插入，只触发一次重排

// DocumentFragment 的特点：
// 1. 轻量的文档对象，不在实际 DOM 树中
// 2. 子节点插入 DOM 后，Fragment 变空（节点被移走）
// 3. 只触发一次重排，性能远优于逐个插入
```

------

### 6.3 MutationObserver ⭐

JavaScript



```
// 监听 DOM 变化（替代已废弃的 Mutation Events）

const observer = new MutationObserver((mutations) => {
  mutations.forEach(mutation => {
    switch (mutation.type) {
      case 'childList':
        console.log('子节点变化');
        console.log('新增:', mutation.addedNodes);
        console.log('移除:', mutation.removedNodes);
        break;
      case 'attributes':
        console.log('属性变化:', mutation.attributeName);
        console.log('旧值:', mutation.oldValue);
        break;
      case 'characterData':
        console.log('文本内容变化');
        break;
    }
  });
});

// 开始观察
observer.observe(document.getElementById('app'), {
  childList: true,      // 监听子节点增删
  attributes: true,     // 监听属性变化
  characterData: true,  // 监听文本变化
  subtree: true,        // 监听所有后代节点（不只是直接子节点）
  attributeOldValue: true,     // 记录属性旧值
  characterDataOldValue: true, // 记录文本旧值
  attributeFilter: ['class', 'style']  // 只监听指定属性
});

// 停止观察
observer.disconnect();

// 获取所有未处理的变更记录
const records = observer.takeRecords();
```

text



```
使用场景：
1. 监控第三方脚本是否篡改 DOM
2. 实现组件的 DOM 变更响应
3. 编辑器中追踪内容变化
4. 实现自定义指令（Vue 的 v-xxx 部分原理）
5. 性能监控中记录 DOM 操作
```

------

### 6.4 IntersectionObserver ⭐⭐

JavaScript



```
// 监听元素与视口（或祖先元素）的交叉状态
// 核心用途：懒加载、无限滚动、曝光统计

const observer = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    console.log(entry.target);             // 被观察的元素
    console.log(entry.isIntersecting);     // 是否可见
    console.log(entry.intersectionRatio);  // 可见比例 0~1
    console.log(entry.boundingClientRect); // 元素的位置信息
    console.log(entry.intersectionRect);   // 交叉区域的位置信息
    
    if (entry.isIntersecting) {
      console.log('元素进入视口');
    }
  });
}, {
  root: null,            // 参照物，null = 视口
  rootMargin: '0px 0px -100px 0px',  // 扩大/缩小观察区域（提前加载）
  threshold: [0, 0.25, 0.5, 0.75, 1]  // 在哪些可见比例触发回调
});

observer.observe(document.querySelector('.target'));

// 停止观察某个元素
observer.unobserve(element);

// 停止所有观察
observer.disconnect();
```

JavaScript



```
// ===== 实战1：图片懒加载 =====
const lazyObserver = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      const img = entry.target;
      img.src = img.dataset.src;      // 将 data-src 赋值给 src
      img.classList.remove('lazy');
      lazyObserver.unobserve(img);     // 加载后取消观察
    }
  });
}, { rootMargin: '200px' });  // 提前 200px 开始加载

document.querySelectorAll('img.lazy').forEach(img => {
  lazyObserver.observe(img);
});

// ===== 实战2：无限滚动 =====
const sentinel = document.getElementById('scroll-sentinel');  // 底部哨兵元素
const scrollObserver = new IntersectionObserver((entries) => {
  if (entries[0].isIntersecting) {
    loadMoreData();  // 哨兵可见 = 滚动到底部
  }
});
scrollObserver.observe(sentinel);

// ===== 实战3：曝光统计 =====
const trackObserver = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting && entry.intersectionRatio >= 0.5) {
      // 元素 50% 以上可见，上报曝光
      reportExposure(entry.target.dataset.id);
      trackObserver.unobserve(entry.target);  // 只统计一次
    }
  });
}, { threshold: 0.5 });
```

text



```
vs 传统方案：
传统方案：scroll 事件 + getBoundingClientRect()
  - scroll 高频触发，需要节流
  - getBoundingClientRect 触发重排
  - 性能差

IntersectionObserver：
  - 异步执行，不阻塞主线程
  - 浏览器内部优化，性能极好
  - API 简洁
```

------

### 6.5 ResizeObserver

JavaScript



```
// 监听元素尺寸变化（替代 window.resize 的更精细方案）

const resizeObserver = new ResizeObserver((entries) => {
  entries.forEach(entry => {
    const { width, height } = entry.contentRect;  // 内容区域尺寸
    console.log(`元素尺寸变化: ${width} x ${height}`);
    
    // entry.borderBoxSize[0]   → 包含 border + padding
    // entry.contentBoxSize[0]  → 仅内容区
    // entry.devicePixelContentBoxSize[0] → 设备像素尺寸

    // 根据容器宽度响应式调整
    if (width < 600) {
      entry.target.classList.add('compact');
    } else {
      entry.target.classList.remove('compact');
    }
  });
});

resizeObserver.observe(document.getElementById('container'));
resizeObserver.unobserve(element);
resizeObserver.disconnect();
```

text



```
使用场景：
1. 容器查询的 Polyfill（CSS Container Queries 出现之前的方案）
2. 图表/Canvas 自适应容器大小（ECharts resize）
3. 虚拟列表根据容器高度计算可见项数量
4. 响应式组件（不依赖视口宽度，而是依赖父容器宽度）
5. 文本溢出检测

vs window.resize：
- window.resize 只能监听窗口大小变化
- ResizeObserver 可以监听任意元素，更精确
- 元素因为内容变化、flex/grid 布局变化而改变大小时，
  window.resize 不会触发，ResizeObserver 会
```

------

### 6.6 BOM（Browser Object Model）

JavaScript



```
// ========== window ==========
// 全局对象，所有全局变量都是 window 的属性
window.innerWidth;     // 视口宽度（不含滚动条）
window.innerHeight;    // 视口高度
window.outerWidth;     // 浏览器窗口宽度（含工具栏）
window.outerHeight;
window.scrollX;        // 水平滚动距离（= pageXOffset）
window.scrollY;        // 垂直滚动距离（= pageYOffset）
window.devicePixelRatio;  // 设备像素比（DPR），Retina 屏通常是 2 或 3

window.open('https://example.com', '_blank');
window.close();
window.print();

window.scrollTo({ top: 0, behavior: 'smooth' });   // 平滑滚动到顶部
window.scrollBy({ top: 100, behavior: 'smooth' });  // 向下滚动 100px

// 对话框（生产环境少用）
window.alert('提示');
const result = window.confirm('确定?');
const input = window.prompt('请输入');

// ========== location ==========
// URL: https://www.example.com:8080/path/page?name=test#section1
location.href;       // 完整 URL
location.protocol;   // 'https:'
location.host;       // 'www.example.com:8080'
location.hostname;   // 'www.example.com'
location.port;       // '8080'
location.pathname;   // '/path/page'
location.search;     // '?name=test'
location.hash;       // '#section1'
location.origin;     // 'https://www.example.com:8080'

location.assign('url');    // 跳转（有历史记录）
location.replace('url');   // 跳转（替换当前记录，无法后退）
location.reload();         // 刷新
location.reload(true);     // 强制从服务器刷新（跳过缓存）

// URLSearchParams：解析查询参数
const params = new URLSearchParams(location.search);
params.get('name');        // 'test'
params.has('name');        // true
params.set('age', '25');
params.delete('name');
params.toString();         // 'age=25'

// 遍历参数
for (const [key, value] of params) {
  console.log(key, value);
}

// ========== navigator ==========
navigator.userAgent;       // 浏览器 UA 字符串（现在不太可靠）
navigator.language;        // 浏览器语言 'zh-CN'
navigator.languages;       // 语言偏好列表 ['zh-CN', 'en']
navigator.onLine;          // 是否在线
navigator.cookieEnabled;   // Cookie 是否启用
navigator.clipboard;       // 剪贴板 API
navigator.geolocation;     // 地理定位 API
navigator.mediaDevices;    // 媒体设备 API（摄像头/麦克风）
navigator.serviceWorker;   // Service Worker
navigator.sendBeacon(url, data);  // 页面卸载时可靠地发送数据

// 更可靠的特性检测（代替 UA 检测）
navigator.userAgentData?.platform;  // 'Windows'/'macOS' 等（新 API）

// 检测网络状态变化
window.addEventListener('online', () => console.log('网络恢复'));
window.addEventListener('offline', () => console.log('网络断开'));

// ========== history ==========
// 前面已详细讲过
history.length;
history.state;
history.pushState(state, '', url);
history.replaceState(state, '', url);
history.back();
history.forward();
history.go(n);

// ========== screen ==========
screen.width;          // 屏幕宽度（物理像素）
screen.height;         // 屏幕高度
screen.availWidth;     // 可用宽度（排除系统任务栏）
screen.availHeight;    // 可用高度
screen.colorDepth;     // 颜色深度（24位/32位）
screen.orientation;    // 屏幕方向
screen.orientation.type;   // 'landscape-primary' / 'portrait-primary'
screen.orientation.angle;  // 0 / 90 / 180 / 270

// 监听屏幕方向变化
screen.orientation.addEventListener('change', () => {
  console.log('方向:', screen.orientation.type);
});
```

text



```
三大 Observer 总结：
┌──────────────────────┬────────────────────────────────────┐
│ MutationObserver     │ 监听 DOM 变化（增删节点/属性/文本）  │
│ IntersectionObserver │ 监听元素与视口的交叉（可见性）       │
│ ResizeObserver       │ 监听元素尺寸变化                    │
└──────────────────────┴────────────────────────────────────┘

BOM 面试高频点：
1. location.assign vs replace 区别（是否有历史记录）
2. window.scrollY + getBoundingClientRect 计算元素位置
3. navigator.sendBeacon 用于页面关闭时可靠发送数据
4. devicePixelRatio 用于高清屏适配（Canvas DPR 处理）
```

------

> **面试建议**：
>
> - **高频考点**：语义化标签的好处、DOM 树构建过程（关键渲染路径）、重排重绘、IntersectionObserver 懒加载、Web Storage 对比、History API 两种路由模式、async/defer 区别
> - **中频考点**：CSP 安全策略、Service Worker 缓存策略、WebSocket 心跳重连、MutationObserver 原理
> - **加分项**：Web Components（Shadow DOM 样式隔离原理）、无障碍 ARIA、DocumentFragment 性能优化

















有什么具体的知识点想深入了解的吗？😊**答案是：需要的！** 虽然现在框架（React/Vue）很火，但 HTML 基础在校招面试中仍然会考到，而且是基本功的体现。

------

## 📌 校招中 HTML 常考的重点



|      |      |
| ---- | ---- |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |

------

### 2. DOCTYPE 和文档结构（⭐⭐）

```
<!DOCTYPE html>   <!-- 告诉浏览器使用 HTML5 标准模式渲染 -->
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>页面标题</title>
</head>
<body>
  ...
</body>
</html>
```

**面试题：DOCTYPE 有什么作用？**

> 声明文档类型，告诉浏览器用**标准模式（Standards Mode）** 而非 **怪异模式（Quirks Mode）** 渲染页面。怪异模式下盒模型等行为会不一致。

------

### 3. meta 标签（⭐⭐⭐）

```
<!-- 字符编码 -->
<meta charset="UTF-8">

<!-- 移动端适配（⭐必须记住） -->
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">

<!-- SEO 相关 -->
<meta name="description" content="页面描述">
<meta name="keywords" content="关键词1,关键词2">

<!-- HTTP 等价头 -->
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta http-equiv="refresh" content="5;url=https://example.com">

<!-- 禁止缓存 -->
<meta http-equiv="Cache-Control" content="no-cache">
```

------

### 4. 行内元素 vs 块级元素（⭐⭐⭐ 高频）

| 特性     | 块级元素                | 行内元素                   | 行内块元素           |
| -------- | ----------------------- | -------------------------- | -------------------- |
| 独占一行 | ✅                       | ❌                          | ❌                    |
| 可设宽高 | ✅                       | ❌                          | ✅                    |
| 默认宽度 | 父容器100%              | 内容宽度                   | 内容宽度             |
| 常见标签 | `div, p, h1-h6, ul, li` | `span, a, strong, em, img` | `input, button, img` |

> ⚠️ **注意**：`<img>` 严格来说是**替换元素（replaced element）**，表现类似 inline-block。

------

### ==5. HTML5 新特性（⭐⭐）==

```
<!-- 表单新类型 -->
<input type="email">
<input type="date">
<input type="range">
<input type="color">
<input type="search">
<input type="number" min="0" max="100">

<!-- 表单新属性 -->
<input type="text" placeholder="请输入..." required autofocus>

<!-- 音视频 -->
<video src="video.mp4" controls autoplay muted></video>
<audio src="audio.mp3" controls></audio>

<!-- 画布 -->
<canvas id="myCanvas" width="400" height="300"></canvas>

<!-- 本地存储 (JS API) -->
<!-- localStorage / sessionStorage -->

<!-- 拖拽 API -->
<div draggable="true">可拖拽元素</div>
```

------

### ==6. src 和 href 的区别（⭐⭐）==

```
<!-- src: 嵌入/替换当前元素，会阻塞解析 -->

<script src="app.js"></script>

<img src="photo.jpg">
```

- **“阻塞解析”阻塞的是：HTML 解析（构建 DOM）**
  具体是这种：`<script src="app.js"></script>`（不带 `async/defer`）会让浏览器**暂停解析后面的 HTML**，先去下载并执行 JS，执行完才继续解析。
- **`<img>` 基本不阻塞 HTML 解析**
  它会边解析 HTML 边发起图片请求；但可能影响**页面渲染完成时机**（例如首屏观感、`load` 事件等），不是“卡住 DOM 解析”那种阻塞。

```
<!-- href: 建立链接关系，不会阻塞 -->
<link href="style.css" rel="stylesheet">
<a href="https://example.com">链接</a>
```



------

### ==7. script 标签的 async 和 defer（⭐⭐⭐ 高频）==

```
<!-- 普通：阻塞 HTML 解析 -->
<script src="app.js"></script>

<!-- async：异步下载，下载完立即执行（不保证顺序） -->
<script src="app.js" async></script>

<!-- defer：异步下载，HTML 解析完后按顺序执行 -->
<script src="app.js" defer></script>
```

```
普通:    HTML解析 → 停 → 下载JS → 执行JS → HTML解析继续
async:   HTML解析 ————————————→ HTML解析继续
              ↘ 下载JS → 执行JS ↗  (可能中断HTML)
defer:   HTML解析 ———————————————→ 执行JS
              ↘ 下载JS ——————↗
```

------





# OG

##  Open Graph (OG) 协议 — 链接预览卡片

就是你在**微信/飞书/Slack/Discord/Twitter**等平台分享一个链接时，会自动显示**标题、描述、图片**的那个卡片效果👇

> 🔗 **示例效果：**
> ┌─────────────────────────┐
> │ 🖼️ [预览图片] │
> │ **网站标题** │
> │ 这是页面的描述文字... │
> │ example.com │
> └─────────────────────────┘

### 怎么做？

在 `<head>` 中添加 OG meta 标签：

```
<head>
  <!-- 基础 Open Graph 标签 -->
  <meta property="og:title" content="我的网站标题">
  <meta property="og:description" content="这是网页的描述，会显示在预览卡片中">
  <meta property="og:image" content="https://example.com/preview.jpg">
  <meta property="og:url" content="https://example.com/page">
  <meta property="og:type" content="website">
  <meta property="og:site_name" content="网站名称">

  <!-- 图片尺寸建议 -->
  <meta property="og:image:width" content="1200">
  <meta property="og:image:height" content="630">
</head>
```

### Twitter 有自己的一套（Twitter Cards）

```
<head>
  <meta name="twitter:card" content="summary_large_image">
  <meta name="twitter:title" content="标题">
  <meta name="twitter:description" content="描述">
  <meta name="twitter:image" content="https://example.com/preview.jpg">
  <meta name="twitter:site" content="@你的推特账号">
</head>
```

### OG 图片的要求

推荐尺寸：1200 x 630 px（最通用）
最小尺寸：600 x 315 px
格式：    JPG / PNG
大小：    < 5MB（建议 < 1MB）
==必须是：  绝对路径 URL（https://...）==

