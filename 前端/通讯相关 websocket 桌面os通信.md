# 代理

代理：把你的某些网络请求通过一个中转服务器，做一层转发

这里涉及到三个概念：

zeroOmega：一个浏览器代理插件，

修改host：

拓展：

现代开发中，很多技术栈模糊了代理边界：

# 跨平台

| 方案         | 支持平台                   | 技术栈              | 渲染方式 |
| ------------ | -------------------------- | ------------------- | -------- |
| React Native | iOS、Android、(Web)        | JavaScript/React    | 原生组件 |
| Flutter      | iOS、Android、Web、Desktop | Dart                | 自绘引擎 |
| Electron     | Windows、macOS、Linux      | JavaScript/HTML/CSS | Chromium |
| uni-app      |                            |                     |          |
| web-view     |                            |                     |          |

## 1. **Electron 应用**

```JavaScript
桌面应用（VS Code、Slack）
└── 内嵌 Chromium 浏览器
    └── 运行 Web 技术（HTML/CSS/JS）
```

- 看起来像桌面应用，实际是个"浏览器"，但不受浏览器扩展控制

## 2. **React Native**

```JavaScript
移动应用
└── JavaScript 引擎（JavaScriptCore/Hermes）
    └── 调用原生 API
```

- 写的是 JavaScript，但不是浏览器环境，网络请求是原生实现

## 3. **WebView**

```JavaScript
原生应用
└── 内嵌 WebView（小浏览器）
    └── 显示网页内容
```

- 微信小程序、App 内的 H5 页面
- 是个"阉割版浏览器"
- 代理行为跟随宿主应用

