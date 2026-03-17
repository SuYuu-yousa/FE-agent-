





# package.json

---

## 1. 什么是 `package.json`？

`package.json` 记录了项目的元数据（名称、版本、描述等）以及项目所依赖的第三方包（dependencies）和开发时依赖（devDependencies）。

### 实例

假设我们要写一个工具，它依赖 `lodash`（一个实用的 JavaScript 工具库），并且在开发时使用 `jest` 进行测试。

在空文件夹中运行 `npm init -y` 会快速生成一个默认的 `package.json`：

```json
{
  "name": "my-tool",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

安装依赖,`package.json` 会自动更新，添加 `dependencies` 和 `devDependencies` 字段：

```json

{
  "name": "my-tool",                    // 项目名称：在 npm 上发布时需唯一
  "version": "1.0.0",                    // 项目版本：遵循语义化版本规范
  "description": "",                      // 项目描述：帮助别人了解项目用途
  "main": "index.js",                     // 入口文件：别人引入你的包时默认加载的文件
  "scripts": {                             // 脚本命令：可通过 npm run 执行
    "test": "jest"                         // 执行 npm test 时会运行 jest 测试
  },
  "keywords": [],                          // 关键词：提高项目的可搜索性
  "author": "",                             // 作者信息
  "license": "ISC",                         // 开源许可证
  "dependencies": {                          // 生产依赖：项目运行必需的包
    "lodash": "^4.17.21"                     // 代码运行时要用的工具库
  },
  "devDependencies": {                       // 开发依赖：仅在开发时需要
    "jest": "^27.5.1"                         // 测试框架，生产环境不安装
  }
}
```



### 版本号符号

`^4.17.21` 表示 **兼容 4.x.x 的最新版本**，即安装时允许安装 `4.17.21` 及以上但小于 `5.0.0` 的版本。类似地：

- `~1.2.3`：安装 `1.2.x` 的最新版本（补丁版本）
- `1.2.3`：只安装精确版本 `1.2.3`
- `*`：最新版本

这种灵活的版本规则带来一个问题：不同时间、不同机器上安装的依赖可能不同（比如 `lodash` 发布了新补丁），导致项目行为不一致。为了解决这个问题，就需要 `lock` 文件。

---

## 2. 什么是 `package-lock.json`（或 `yarn.lock`）？

`package-lock.json` 是 **npm 自动生成**的一个文件，它会**精确锁定**当前安装的每个依赖的具体版本号、下载地址和依赖树结构。如果你用 Yarn，则对应 `yarn.lock`。

### 实例：看看 `package-lock.json` 长什么样

执行 `npm install` 后，项目根目录会生成 `package-lock.json`。下面是它的部分内容（已简化）：

```json
{
  "name": "my-tool",
  "version": "1.0.0",
  "lockfileVersion": 2,
  "requires": true,
  "packages": {
    "": {
      "name": "my-tool",
      "version": "1.0.0",
      "license": "ISC",
      "dependencies": {
        "lodash": "^4.17.21"
      },
      "devDependencies": {
        "jest": "^27.5.1"
      }
    },
    "node_modules/lodash": {
      "version": "4.17.21",
      "resolved": "https://registry.npmjs.org/lodash/-/lodash-4.17.21.tgz",
      "integrity": "sha512-...",
      "license": "MIT"
    },
    "node_modules/jest": {
      "version": "27.5.1",
      "resolved": "...",
      "integrity": "...",
      "dev": true,
      "dependencies": {
        "@jest/core": "^27.5.1",
        "jest-cli": "^27.5.1"
      }
    },
    "node_modules/@jest/core": {
      "version": "27.5.1",
      "resolved": "...",
      "integrity": "...",
      "dev": true,
      "dependencies": {
        "@jest/console": "^27.5.1",
        // ... 等等，会记录所有子依赖的精确信息
      }
    }
    // ... 更多嵌套依赖
  }
}
```

### 为什么需要 lock 文件？

1. **确保一致性**  
   假如你的同事或 CI 服务器在另一个时间运行 `npm install`，如果 `lodash` 发布了 `4.17.22`，根据 `^4.17.21` 规则，他会安装 `4.17.22`。如果新版本有破坏性变化，你的项目可能就挂了。而 `package-lock.json` 强制安装 `4.17.21`，保证所有人环境一致。

   

---

## 3. 两者如何协同工作？

- **`package.json`**：定义**允许的版本范围**（如 `^4.17.21`）。
- **`package-lock.json`**：锁定**实际安装的精确版本**（如 `4.17.21`）以及整个依赖树。

当你运行 `npm install` 时：

- 如果 `node_modules` 不存在且 lock 文件存在，npm 会按照 lock 文件安装精确版本。
- 如果你修改了 `package.json`（比如升级依赖），运行 `npm install` 会更新 lock 文件，记录新安装的精确版本。
- 如果你删除了 lock 文件，npm 会根据 `package.json` 重新解析最新符合范围的那些版本，生成新的 lock 文件。

### 最佳实践

- **将 `package-lock.json` 提交到 Git**，确保团队成员和部署环境使用完全相同的依赖版本。
- 不要手动修改 lock 文件，由 npm 自动管理。
- 对于库作者（发布的 npm 包），通常不提交 lock 文件（但近年也有争议，一般建议提交以方便贡献者）。

---

## 4. 实际场景举例

假设你正在开发一个应用，`package.json` 中依赖了 `express`（版本 `^4.18.0`）。某天 express 发布了 `4.18.1`（修复了一个安全问题），但同时也引入了一个与你代码不兼容的小改动。如果没有 lock 文件，新部署的服务器会自动安装 `4.18.1` 导致应用崩溃。有了 lock 文件，所有环境都会继续使用 `4.18.0`，给你时间测试后再决定是否升级。

---

## 总结

- **`package.json`**：项目的**清单**，列出项目需要哪些包以及大致版本范围，还包含项目信息、脚本等。
- **`package-lock.json`**：安装的**快照**，记录每个依赖的精确版本和依赖树，确保项目在任何机器上都能安装出完全相同的 `node_modules`。

