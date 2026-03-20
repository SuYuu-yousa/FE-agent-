# HTML

## 🎯 校招复习建议

```
重要程度排序：

🔴 必须掌握：语义化、行内/块级、async/defer、meta viewport、本地存储区别
🟡 建议掌握：DOCTYPE、src/href、HTML5 新特性、Canvas/SVG
🟢 了解即可：iframe、Web Components、无障碍（ARIA）
```

> **实话说**：纯 HTML 题在面试中占比不大（大概 5-10%），但如果答不上来会**很减分**，因为面试官会觉得基础不扎实。建议花 **1-2天** 快速过一遍就够了，重点精力放在 **JS、CSS、框架、计算机网络、手写题** 上。

------

有什么具体的知识点想深入了解的吗？😊**答案是：需要的！** 虽然现在框架（React/Vue）很火，但 HTML 基础在校招面试中仍然会考到，而且是基本功的体现。

------

## 📌 校招中 HTML 常考的重点

常见语义化标签：

| 标签                        | 用途             |
| --------------------------- | ---------------- |
| `<header>`                  | 页头/区块头部    |
| `<nav>`                     | 导航             |
| `<main>`                    | 主要内容（唯一） |
| `<article>`                 | 独立内容块       |
| `<section>`                 | 内容分区         |
| `<aside>`                   | 侧边栏           |
| `<footer>`                  | 页脚             |
| `<figure>` / `<figcaption>` | 图片+说明        |
| `<time>`                    | 时间             |
| `<mark>`                    | 高亮文本         |

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



### 8. 常见面试题精选

#### ① 如何理解 HTML 中的空元素（void elements）？

```
<!-- 没有闭合标签的元素 -->
<br> <hr> <img> <input> <link> <meta>
```

#### ② iframe 的优缺点？

```
优点：隔离样式/JS、嵌入第三方页面
缺点：阻塞主页面 onload、不利于 SEO、安全风险（XSS/点击劫持）
```

#### ③ Canvas vs SVG？

| Canvas               | SVG                |
| -------------------- | ------------------ |
| 基于像素（位图）     | 基于矢量           |
| JS 绘制              | XML 描述           |
| 不支持事件绑定到图形 | 每个元素都可绑事件 |
| 适合游戏/大量图形    | 适合图标/图表      |
| 放大失真             | 放大不失真         |

#### ④ 本地存储的区别？

| 特性         | Cookie    | localStorage | sessionStorage |
| ------------ | --------- | ------------ | -------------- |
| 大小         | ~4KB      | ~5MB         | ~5MB           |
| 过期         | 可设置    | 永久         | 页面关闭清除   |
| 发送到服务器 | ✅每次请求 | ❌            | ❌              |
| 作用域       | 同源+路径 | 同源         | 同源+同标签页  |

#### ⑤ 如何做 SEO 优化？（HTML 层面）

- 合理使用语义化标签
- 正确使用 `h1-h6` 层级
- `img` 添加 `alt` 属性
- 设置 `title`、`meta description`
- 使用 `<a>` 的 `rel="nofollow"`
- 结构化数据（JSON-LD）



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

```
推荐尺寸：1200 x 630 px（最通用）
最小尺寸：600 x 315 px
格式：    JPG / PNG
大小：    < 5MB（建议 < 1MB）
必须是：  绝对路径 URL（https://...）
```