# 包管理器（npm / yarn / pnpm）

> 目标：面试常问「npm/yarn/pnpm 区别」「pnpm 原理」「lock 文件」「semver」。下面已填好答案，可照着背 + 用自己的话复述。

## 一、npm / yarn / pnpm 区别 ⭐

### 安装速度与磁盘
npm 用扁平 node_modules，易产生依赖冲突和「幽灵依赖」；yarn 更快、锁文件更早出现；pnpm 用硬链接指向全局 store，同版本包只存一份、最省磁盘。

### 一句话
现在新项目基本用 pnpm（快 + 省盘），npm 保底，yarn 逐渐少用。

## 二、pnpm 原理 ⭐

### 硬链接 / 符号链接
所有包实际存在全局 store（`~/.pnpm-store`），项目 node_modules 用硬链接/软链指向它，同一版本只存一份，所以装得飞快。

### pnpm install 做了什么
读 package.json + .npmrc → 解析依赖树（resolved）→ 查全局缓存（reused）→ 下载缺失（downloaded）→ 建 node_modules（硬链/软链）→ 跑 postinstall → 生成 pnpm-lock.yaml。

### 幽灵依赖
npm 扁平化导致你能 require 到没直接声明的包（A 依赖 B，你也能 require B）；pnpm 严格目录结构，只能用你声明过的依赖，避免「没装却能用」的坑。

## 三、lock 文件

### 作用
锁定精确版本，保证团队/CI 装出来完全一致。三个：package-lock.json / yarn.lock / pnpm-lock.yaml，必须提交到 git。

## 四、semver 版本号

### ^ 和 ~
`^1.2.3` = 1.x.x（允许更新次版本和补丁）；`~1.2.3` = 1.2.x（只更新补丁）。主版本为 0 时（0.x）规则更保守。

## 五、npm ci vs npm install
`npm ci` 严格按 lock 装、先删 node_modules、更快、用于 CI 保证一致；`install` 会更新 lock、用于日常开发加依赖。

## 六、dependencies 分类

### dependencies / devDependencies / peerDependencies
dependencies 运行时依赖（react）；devDependencies 开发依赖（vite、eslint）；peerDependencies 宿主依赖（某 react 插件声明「需要 react 17+」，由使用方提供）。

## 七、.npmrc
换源（registry）、scope 私有源（`@公司:registry`）、二进制镜像（sass/phantomjs）、side-effects-cache 等配置。
