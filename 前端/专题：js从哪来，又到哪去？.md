

首先是写。原生三大件时代，我们直接在 HTML 里写要用到的 JS 文件，用 defer / async 控制加载时机和顺序。

现在用框架，本质就是用更好的写法写三大件，最后交给打包工具还原成「浏览器能跑的三大件」。

以 React 为例：我们写的时候最后只有一个主入口（一个 HTML + 一个主 JS）。这个主 JS（可能是 tsx）里，通过 import 引用其他文件。import 分两种：

- 静态 import → 编译期就确定，打包时合并进主 bundle，不拆
- 动态 import()（比如React.lazy 底层就是它）→ 打包时才拆成独立 chunk

`// 静态 import：在顶部，编译期确定`
`import Home from './Home'`

`// 动态 import：在代码里，运行到才加载`
`const Settings = React.lazy(() => import('./Settings'))  // lazy 底层`
`// 或直接：`
`const load = () => import('./Settings')  // 返回 Promise`

所以打包工具是看「是不是动态 import」来拆的，把代码拆成好几个 chunk，放服务端 / CDN 上。


实际工程长这样

`// 主入口 index.js —— 只静态 import 一个 App`
`import App from './App'`

`// 路由文件里 —— 每个路由一个懒加载，这就是"分割点"`
`const Home     = lazy(() => import('./pages/Home'))`
`const Settings = lazy(() => import('./pages/Settings'))`
`const User     = lazy(() => import('./pages/User'))`

`每个 import('./pages/xxx') 就是一个分割点 → 拆一个 chunk。 这些分割点分散在各路由/组件文件里，不是主入口里列一个清单。`


消费时：

- 主 JS 是 type="module"，行为 = defer：异步下载、不阻塞解析、等 DOM 解析完再执行（首屏代码走这条）
- 主 JS 执行到 React.lazy（动态 import）时，运行时动态创建 script 去拉对应 chunk——行为像 async（不等 DOM、拉完就跑），但它不是纯 async：chunk 之间如果有 import 依赖，运行时（runtime + 模块映射表，打包工具做的）会用 Promise 链保证依赖先加载完再执行

所以：主屏是 defer；其他 chunk 是「动态 import 加载，行为像 async，但依赖有序」。



一句话：==没有动态 import = 没做「按需分割」，首屏会把所有代码（可能 entry + vendor 两个文件）一次性下完；至于拆成几个文件，那是工具默认的 vendor 优化，跟"代码分割"不是一回事。==


- entry（你的代码）
- vendor（第三方库）——而且第三方库可能还被拆成好几个 vendor（比如 React 一个、antd 一个），但概念上都是"别人的代码"这一拨。

▎ SDK 看引入方式：npm import 的 → vendor（进包）；script 标签 CDN 引的 → 打包之外，独立加载（走 async）。


# JS与性能优化

1. 打包是干嘛的

你写代码是 import / export 一堆模块，但浏览器不是所有情况都认（尤其老浏览器、还有非 ESM 的库）。打包器（Vite/Webpack）做的事就一件：把你这一堆模块 + 依赖，翻译并合并成一个（或几个）JS 文件，浏览器直接能跑。

2. bundle 是什么

就是上面合并出来的那个文件。你项目里几十上百个 .js/.tsx，最后变成 index.abc123.js 这么一个东西。

3. 问题来了：一个 bundle 会越来越大

项目一大，全塞一个文件里 → 文件几个 MB → 用户打开页面要一次性下完并解析才出画面 → 首屏慢。

这就是为什么「打包」和「性能」挂钩了。

4. 代码分割（Code Splitting）：拆小

思路很简单：别一次性全给，拆成多个小文件，用到哪个才拉哪个。

- 主 chunk：首屏必需的代码
- 其他 chunk：路由、组件、重库，用到才下载

5. 它怎么省性能

用户进首页 → 只下载「首页必需的代码」→ 秒开。
点了「设置」页 → 才去下设置页的代码。
没点的功能，代码压根不下载。

首屏要下的东西少了 → 加载快、解析快。

6. React 里怎么写

// 懒加载组件：切到才下载
const Settings = React.lazy(() => import('./Settings'))

// 配合 Suspense 兜底加载中状态

`<Suspense fallback={<Spin />}>`
  `<Settings />`
`</Suspense>`


import() 是动态导入，打包器看到它就自动把这个模块拆成一个独立 chunk。

7. 工程里常见分割策略

- 路由级：每个路由一个 chunk（最常见）
- 组件级：重组件（图表、富文本编辑器）懒加载
- 第三方库单独拆：React、antd 这些不常变的单独成 chunk，好缓存

---


# JS


▎ 打包把模块合成 bundle → bundle 太大首屏慢 → 代码分割把 bundle 拆成按需加载的小 chunk → 首屏只下必要的，性能就上来了。


打包产物在 HTML 里长这样

`<script type="module" src="/assets/index.abc123.js"></script>`

主入口就一个 <script type="module"/>。
type="module" 的行为就是 defer——异步下载、不阻塞解析、等 DOM 解析完执行。

代码分割的 chunk 是怎么加载的

主入口（index.js）执行到 React.lazy(() => import('./Settings')) 这行时，运行时动态再创建一个 script 标签去拉 Settings 的 chunk。这其实就是

▎ 动态 createElement('script') 默认 async 行为

的工程化体现，这是react做的事情。所以 chunk 是用到才拉、拉完就跑，没有固定声明顺序。

整体流程（用户打开页面到 JS 跑起来）

1. 请求 HTML
2. 解析 HTML，遇到 <script type="module" src="index.js">
3. 不阻塞 → 一边继续解析 HTML，一边异步下载 index.js
4. HTML 解析完（DOM 建好）
5. 执行 index.js（bundle 内部按 import 依赖图顺序初始化）
6. 执行中遇到动态 import → 再去下载 Settings 的 chunk
7. chunk 下载完 → 执行 → 渲染对应组件

顺序到底谁决定（分三层）

![[Pasted image 20260831215708.png]]

一句话串起来：

▎ defer 负责「主入口等 DOM 再跑」，import 依赖图负责「bundle 内部顺序」，动态 import 负责「chunk 用到才拉」。

所以 async/defer 和打包不是两套东西——打包器只是把浏览器这套加载机制（defer + 动态加载）替你编排好了，你写 import/lazy，它翻译成对应的 script 加载行为。

大厂实操里最常见的组合

路由级（骨架）+ 重组件级（图表/编辑器/视频）+ vendor（公共库）

一句话总结：

▎ 按「什么时候用到」来分：路由到才下 → 路由 chunk；组件滚到/点开才用 → 组件 chunk；公共库一直要 → vendor 走缓存。