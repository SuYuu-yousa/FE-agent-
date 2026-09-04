# 构建工具（Vite / Webpack / 新一代 Rust 工具）

> 目标：面试高频「Vite 为什么快」「loader vs plugin」「tree-shaking」。2024-2026 新增 Rust 浪潮。下面已填好答案，可照着背 + 用自己的话复述。

## 一、为什么需要构建工具

### 浏览器不认识什么
浏览器原生只能跑 HTML/CSS/JS（且是旧语法）。你写的 `import/export` 模块化、TS、JSX、less/scss、图片、静态资源，浏览器都不认。构建工具把它们「编译 + 打包」成浏览器能跑的产物——`npm run build` 干的就是这事。

## 二、Vite vs Webpack ⭐

### Webpack 的问题
启动时先把所有模块**全量打包**，项目一大（几百上千文件）启动慢、热更新慢；配置一堆 loader/plugin 门槛高。老项目大多是它，你会遇到「改一行等几秒」。

### Vite 为什么快
开发时**不打包**：① 依赖用 esbuild 预打包（Go 写的，快几十倍）；② 源码走浏览器原生 ESM 按需加载，改哪个加载哪个；③ 热更新只替换改动模块。生产构建再交给 Rollup（Vite 7 换 Rolldown）。

### 一句话对比
Webpack 打包一切、兼容强、但慢；Vite 借原生 ESM 免打包、快、但要求现代浏览器（IE 不支持，已淘汰）。

## 三、核心概念

### loader vs plugin ⭐
loader 做**文件转换**（less→css、ts→js、图片→base64），针对单个文件；plugin 做**流程增强**（压缩、生成 HTML、注入环境变量），针对整个构建。例：import 一个 `.less` 靠 less-loader 转成 CSS；build 完压缩靠 TerserPlugin、生成 index.html 靠 HtmlWebpackPlugin。

### tree-shaking
靠 ESM 的**静态分析**，剔除没被 import 的死代码。条件：ESM + 生产模式 + 声明副作用。例：`import { debounce } from 'lodash'` 只打包 debounce，其余几百 KB 不进来。

### 代码分割 code splitting
用动态 `import()` 把代码拆成多个 chunk，**按需加载**。典型场景：路由懒加载（`React.lazy`），进 `/admin` 才加载 admin chunk，首屏只加载当前页。

### 热更新 HMR 原理
文件变了只更新对应模块并通知浏览器**替换**、不整页刷新。Vite 靠 ESM + WebSocket 推送。价值：改 CSS 立即生效、表单 state 不丢、不用刷新重来。

## 四、新一代 Rust 工具（2024-2026）⭐

### 为什么都换 Rust
esbuild（Go）证明原生速度能快几十倍（webpack 20s → esbuild 200ms）；Rust 内存安全 + 极致性能，于是 SWC/Rspack/Turbopack/Rolldown 全用 Rust 重写。

### Rspack（字节跳动）
Webpack 的**平替**，配置几乎兼容（90% 直接搬）、快 5-10 倍；Rsbuild 是更友好的上层。老 Webpack 项目想提速首选迁它。

### Turbopack（Vercel）
**Next.js 专用**构建引擎，函数级缓存做到近即时 HMR；不是通用打包器，别的框架用不了。

### Rolldown
更快的 **Rollup**，Vite 7 的新引擎（dev/prod 统一），发库可考虑。

### 选型一句话
新项目 → Vite；Next.js → Turbopack；老 Webpack → Rspack；发 npm 库 → Rollup/Rolldown；纯转译/CI 提速 → esbuild。

## 五、webpack 基础（维护老项目会用到）

### entry / output / loader / plugin
四要素：entry 入口、output 产物、loader 转文件、plugin 增流程。会看老项目配置即可。

### 常见优化
代码分割、第三方抽 CDN、文件名加 hash 做长缓存、持久化缓存、`splitChunks`。




