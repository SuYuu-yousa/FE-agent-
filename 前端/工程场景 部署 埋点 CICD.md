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



# ABtest

- 