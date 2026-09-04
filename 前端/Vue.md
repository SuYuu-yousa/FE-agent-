# Vue 面试大纲

> 目标：JD 要求 React/Vue 二选一或都会。Vue 面试聚焦响应式原理、生命周期、组件通信、与 React 对比。已填好答案。

## 一、Vue 是什么

### MVVM 与数据驱动
Vue 是渐进式框架（核心 + 按需加路由/状态管理）；MVVM 模式（Model-View-ViewModel）；数据驱动视图——改数据，视图自动更新，不用手动操作 DOM。

## 二、响应式原理 ⭐

### Vue 2：Object.defineProperty
用 defineProperty 拦截对象属性的 getter/setter；坑：数组下标、新增属性监听不到，要 `$set`。所以 Vue2 里 `this.arr[0]=x` 不触发更新。

### Vue 3：Proxy
用 Proxy 拦截整个对象，支持数组、新增属性、惰性追踪；为什么换：性能更好 + 解决 Vue2 的数组/新增属性缺陷。

### ref vs reactive
`ref` 包装基本类型，访问要 `.value`；`reactive` 包装对象，直接访问。简单记：基本类型用 ref，对象用 reactive（或统一用 ref）。

## 三、生命周期 ⭐

### 常用钩子
Vue3 常用：`onMounted`（挂载完，适合发请求/操作 DOM）、`onUpdated`、`onUnmounted`（清理定时器/监听）；before 系在之前执行。面试重点：onMounted 发请求。

## 四、模板语法与指令

### v-if vs v-show
v-if 条件渲染、真销毁重建（切换成本高）；v-show 只切 `display`（初始渲染成本高）。频繁切换用 v-show，一次性/条件不常用 v-if。

### v-for 与 key
key 不能用 index（列表增删/排序时复用错乱）；v-for 和 v-if 不能同节点（v-if 优先级更高，导致 v-if 里拿不到 v-for 的变量），用 computed 过滤代替。

## 五、组件通信 ⭐

### props / emit
父传子 props、子传父 emit；Vue3 用 `defineProps`/`defineEmits`。

### provide / inject
跨层级传递（祖先 provide、后代 inject）；不适合复杂状态管理（没有细粒度响应式追踪）。

### pinia / vuex
全局状态管理；pinia 更简单（setup store、去 mutation）；vuex 偏旧。Vue3 默认推荐 pinia。

## 六、computed vs watch
computed 有缓存、依赖变了才重算（适合派生值）；watch 监听副作用（异步/请求/节流）。例：全名 = 姓 + 名用 computed；搜索输入变化发请求用 watch。

## 七、nextTick
DOM 更新是**异步**的，`nextTick`（或 `await nextTick()`）等更新完再操作 DOM。例：改完 state 想立刻拿最新 DOM 高度，就放 nextTick 里。

## 八、组合式 vs 选项式
Composition API（setup，逻辑按功能聚合、复用方便）vs Options API（data/methods 按类型分组、新手友好）。Vue3 推荐组合式。

## 九、Vue vs React ⭐
响应式：Proxy 自动追踪 vs 手动 setState；模板 vs JSX；v-model 双向 vs 受控单向；生态。一句话：Vue 上手快、模板直观；React 灵活、生态大。都会更好（JD 要求 React/Vue）。

## 十、性能与原理

### 虚拟 DOM 与 diff
Vue 的 diff 优化（静态提升、patchFlag 跳过静态节点）；key 帮助 diff 复用节点。
