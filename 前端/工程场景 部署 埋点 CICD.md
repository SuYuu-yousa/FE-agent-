# 环境

| 环境          | 作用               | 特点                           |
| ------------- | ------------------ | ------------------------------ |
| 开发环境 Dev  | 开发写代码、联调   | 本地跑，配置随意，常有跨域     |
| 测试环境 Test | 给测试同学验证功能 | 接近线上，但数据/稳定性一般    |
| 集成环境 SIT  | 多服务集成联调     | 看前后端、上下游能不能一起跑通 |
| UAT / 预发    | 上线前最终验证     | 环境、配置、流程尽量接近生产   |
| 生产环境 Prod | 真正给用户使用     | 最稳定，最严格，不能乱动       |

泳道？





# CICD

```
CI/CD & 部署
├── GitHub Actions / GitLab CI
│   ├── 工作流编写（on/jobs/steps/matrix）
│   ├── 缓存策略（actions/cache）
│   ├── 环境变量与 Secrets 管理
│   └── 自动化：lint → test → build → deploy 流水线
├── 现代部署模式 ⭐（新增）
│   ├── Preview Deployments（PR 预览环境）
│   │   └── Vercel/Netlify 的 PR 自动部署预览
│   ├── Edge Runtime / Edge Functions
│   │   ├── Vercel Edge Functions / Cloudflare Workers
│   │   ├── 与 Serverless Functions 的区别
│   │   └── 适用场景（A/B测试/地理位置路由/认证）
│   ├── ISR（Incremental Static Regeneration）
│   │   └── Next.js 的按需重新生成静态页面
│   ├── 蓝绿部署 / 金丝雀发布（概念理解）
│   └── Feature Flags（功能开关）
├── Docker 基础
│   ├── Dockerfile 多阶段构建（构建阶段 + nginx 阶段）
│   ├── .dockerignore
│   └── docker-compose（前端 + 后端 + 数据库）
├── Nginx ⭐
│   ├── 静态资源服务 & gzip/brotli
│   ├── 反向代理 & 负载均衡
│   ├── HTTPS / HTTP2 配置
│   ├── History 路由 try_files
│   ├── 缓存头配置（hash 文件长缓存 + HTML 不缓存）
│   └── 跨域配置（add_header）
└── Serverless
    ├── 概念与适用场景
    ├── Vercel Serverless Functions / AWS Lambda
    └── 冷启动问题与优化
```

# 埋点

### 什么是埋点
在关键行为/节点上报数据（点击、曝光、进入页面），供产品分析、数据驱动决策。前端岗基本都会接触——埋点是「数据驱动」的入口。

### 常见埋点类型
代码埋点（手动调上报，精确但累）、可视化埋点（圈选配置）、无埋点/全埋点（自动采集）；最常见两类：曝光埋点（用 IntersectionObserver 检测元素进视口）、点击埋点。

### 埋点上报方案
图片 src 打点（老、跨域简单）、`navigator.sendBeacon`（页面关闭/跳转时可靠上报，埋点首选）、fetch；工程上做批量上报 + 采样，封装成埋点 SDK 统一调用。

### 埋点体系
事件模型（谁、什么时候、做了什么、上下文）、公共参数（uid/设备/来源/版本）、唯一事件名规范（如 `click_submit_btn`）。

# ABtest

### 是什么
同一功能多个版本，随机分给不同用户组，比数据决定用哪个；A/B/n。大厂迭代标配。

### 前端怎么做
服务端下发实验分组（或 SDK 分流）→ 前端按 flag 渲染不同版本 → 上报实验数据 → 分析胜出。前端核心就是「读 flag 渲染对应版本 + 上报」。

### 与 Feature Flag 的关系
ABtest 通常靠 Feature Flag（功能开关）实现灰度/分流——同一个开关既能灰度也能 A/B。