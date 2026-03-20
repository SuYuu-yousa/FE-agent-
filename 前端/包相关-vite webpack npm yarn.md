# 包管理器

Pnpm install做了什么

比如说

Progress: resolved 1256, reused 1163, downloaded 0, added 0

所以 node_modules 是给 Node.js 用的查找路径，不是真正存储包的地方。

```Plain
pnpm install
    ↓
1. 读取 package.json + .npmrc
    ↓
2. 解析依赖树（resolved 1259）
    ↓
3. 检查全局缓存（reused 1123）
    ↓
4. 下载缺失的包（downloaded 136）
    ↓ 保存到 ~/Library/pnpm/store/v3
    ↓
5. 创建 node_modules 结构（added 1163）
    ├─ 硬链接到全局缓存
    └─ 符号链接到虚拟存储
    ↓
6. 运行 postinstall 脚本
    ↓
7. 生成 pnpm-lock.yaml
    ↓
✅ Done
```

`.npmrc` 是 npm/pnpm/yarn 的配置文件，用来设置包管理器的行为。

以下面这个为例

```Plain
registry=http://registry.npm.bilibili.co
phantomjs_cdnurl=http://cnpmjs.org/downloads
sass_binary_site=https://npm.taobao.org/mirrors/node-sass/
@bilibili:registry=http://registry.npm.bilibili.co
@jinkela:registry=http://registry.npm.bilibili.co
@plat-components:registry=http://registry.npm.bilibili.co
side-effects-cache=false
```

设置特定 scope（命名空间）的包从哪里下载

例如 @bilibili/xxx 这样的包会从 B 站内部源下载

3. phantomjs_cdnurl / sass_binary_site

设置二进制文件的下载镜像地址

因为某些包（如 phantomjs、node-sass）需要下载编译好的二进制文件

4. side-effects-cache=false

禁用副作用缓存，确保每次安装都执行完整的安装脚本



## Npm ci:



# package.json

我一般理解package.json是一个包管理记录表

但其实



# 八股问题



js事件流



## 好用插件

Vscode Gitlens可以看某一行是谁什么时候在哪个commit提交的

浏览器的mod header可以修改 HTTP 请求头和响应头，比如添加自定义 Authorization、修改 User-Agent、调试 CORS、切换 Cookie 等，支持按 URL 过滤，方便测试不同环境 浏览器的zeroomega可以快速管理和切换多个代理配置，支持手动切换、按域名自动匹配规则、PAC 脚本分流，适合开发测试、跨境协作等场景 。