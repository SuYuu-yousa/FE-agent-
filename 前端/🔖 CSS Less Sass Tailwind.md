# CSS 基础常考点

## 选择器

| 选择器             | 例子                      | 选中的是                                                                                                                                                                                          |
| --------------- | ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 元素              | `div`                   | 页面上所有 div                                                                                                                                                                                     |
| 类               | `.box`                  | 所有 `class="box"` 的                                                                                                                                                                            |
| id              | `#app`                  | 唯一那个 `id="app"` 的                                                                                                                                                                             |
| 通配符             | `*`                     | 所有元素                                                                                                                                                                                          |
| 后代              | `ul li`                 | ul 里面的 li，隔几层都算                                                                                                                                                                               |
| 子代              | `ul > li`               | ul 的直接儿子 li，只一层                                                                                                                                                                               |
| 相邻兄弟            | `a + span`              | a 后面紧挨着的那一个 span                                                                                                                                                                              |
| 通用兄弟            | `a ~ span`              | a 后面所有 span                                                                                                                                                                                   |
| 属性              | `[type="text"]`         | 有 `type="text"` 的 input                                                                                                                                                                       |
| 属性              | `[href^="https"]`       | href 以 https 开头的链接                                                                                                                                                                            |
| `:hover`        | `a:hover`               | 鼠标悬停的 a                                                                                                                                                                                       |
| `:focus`        | `input:focus`           | 正在聚焦（光标在）的 input                                                                                                                                                                              |
| `:nth-child(n)` | `li:nth-child(2)`       | 第 2 个 li                                                                                                                                                                                      |
| `:first-child`  | `li:first-child`        | 第一个 li                                                                                                                                                                                        |
| `:last-child`   | `li:last-child`         | 最后一个 li                                                                                                                                                                                       |
| `:not()`        | `div:not(.box)`         | 不是 .box 的 div                                                                                                                                                                                 |
| `::before`      | `.tag::before`          | 元素前面画个假元素                                                                                                                                                                                     |
| `::after`       | `.tag::after`           | 元素后面画个假元素                                                                                                                                                                                     |
| `:has()`        | `.card:has(img)`        | 里面有图的卡片（反向选父）                                                                                                                                                                                 |
| `:is()`         | `:is(.class1, .class2)` | 是class1 或 2的（优先级取最高）括号里三个：:is(#app, .btn, div) { color: red; }<br>- #app 是 id，权重 (1,0,0)  最猛<br>- .btn 是类，(0,1,0)<br>- div 是元素，(0,0,1)<br><br>:is() 取最高的那个 → 整条权重按 #app 的 (1,0,0) 算，跟 id 一样猛。 |
| `:where()`      | `:where(h1, h2)`        | h1 或 h2（优先级 0）                                                                                                                                                                                |

### 基础选择器
元素、类 `.box`、id `#app`、通配符 `*`
### 组合选择器
后代 `ul li`、子代 `ul > li`、相邻兄弟 `a + span`、通用兄弟 `a ~ span`
### 属性选择器
`input[type="text"]`、`[href^="https"]` 等。
### 伪类

`:hover / :active / :focus / :visited / :first-child / :nth-child(n)` 各代表什么。

:focus   鼠标键盘选中，如进入Input
:visited  链接点过了
:active  鼠标按下不抬起
#### :has() 父级选择器 ⭐
根据子元素状态反向控制父元素，如 `.form:has(input:invalid)`；`body:has(.modal-open)`；典型场景（表单校验、弹窗锁滚动、区分有图/无图卡片）。
### :is() vs :where()
`:is()` 特异性取参数中最高的；`:where()` 特异性恒为 0（适合做可覆盖的默认样式）。
```

.list a:hover,
.list button:hover,
.list .icon:hover { color: red; }

用 :is() 合并，前缀写一次：

.list :is(a, button, .icon):hover { color: red; }


:where(.btn) { background: #eee; } 就是：

- 全局所有 .btn 这个类
- 默认给个背景色
- 权重最低（0）
```

### 伪元素 `::before/::after`；
两者区别。
一个 `<div>`（块级元素）加上 ::before，它出现在 div 盒子的内部最顶部；::after 在内部最底部。

```
<div class="tag">标签</div>
.tag::before { content: "● "; }
.tag::after  { content: " ✕"; }
```

渲染出来就是：● 标签 ✕，

我们经常用:before  :after做悬浮，他们本质上是用css加一个dom  ×
加的这个玩意没dom，纯看，f12选中他发现是他本身绑的元素

## 优先级

### 权重计算
`!important` > 行内内联 > id > 类/伪类/属性 > 元素/伪元素 > 通配符/继承。
>口诀：==恋爱累圆通==   内敛 id  类和伪类  元素和伪元素  通配符

如果是react，内敛和类 伪类  伪元素这些是怎么绑定的？毕竟他们都是虚拟dom，我学css时他们都是原生HTML去绑定
### 覆盖规则
权重相同时"后写的覆盖先写的"；`!important` 尽量少用。






## 盒模型

### 标准 vs 怪异
标准盒模型 `width` 只算 content；
怪异盒模型（`box-sizing: border-box`）`width` 包含 padding + border。

现在基本上用怪异

三层：context  border paddding  margin

==开启怪异盒模型地一句话： box-sizing: border-box==



## 居中

### 六大方法：
==flex布局
grid布局
relative + margin auto
absolute + top left 5050 +translate  -50-50
inline-height  text align==
### 水平居中
块级 `margin: 0 auto`、行内/文本 `text-align: center`、flex `justify-content: center`。
### 垂直居中
flex `align-items: center`、单行文本 `line-height = height`。
### 水平垂直居中
`position: absolute + transform: translate(-50%,-50%)`、flex 两属性同用、grid `place-items: center`。






## BFC ⭐外界无关区域

 BFC 是 CSS 规范定义的、由浏览器渲染引擎实现的一套布局规则（算法）。 当元素满足触发条件时，浏览器就按这套规则计算它的渲染。

表现：里面元素自己算自己的布局，跟外面互不渗透。

"互不影响"具体指布局规则这三条：
1. 自己的 margin 不漏出去（外边距折叠）
2. 内部浮动不外溢（高度能包住浮动子元素）
3. 不跟外面的浮动重叠

### 触发条件
==`overflow` 非 visible、`display: flow-root`、`position: absolute/fixed`、`float` 非 none、`contain: layout`。

任选其一
- 规则是谁定的：W3C 的 CSS 规范（写死了"BFC 有哪些行为"）。
- 谁来执行：浏览器的渲染引擎（Chrome 是 Blink、Safari 是 WebKit、Firefox 是 Gecko）。

你写 overflow:hidden，浏览器解析时发现"这值满足 BFC 条件"，就自动把这块按 BFC 的布局算法来排——算高度时把浮动儿子算进去、margin 不折叠、不跟 float 重叠。


contain: layout是什么
display flow-root是什么  

### 应用场景
解决外边距折叠、清除浮动（包含浮动子元素）、两栏自适应防重叠。

为什么他能解决外边距折叠？
正常两个元素之间的margin会取大的那个
两个bfc区域上下挨着，如果没有bfc，上面写margin bottom 20 下面30,间隔会是30,加了bfc是50

清除浮动（底层）
父高度是 auto = "儿子多高我多高"。但 float 的儿子"飘走"不占位，父算高度时不算他 → 塌到 0。
BFC 有条死规矩：==算自己高度时，必须把里面浮动的儿子也算进去==。所以父一变 BFC，就把飘走的儿子重新"算进来"，==高度撑回来了==。
![[Pasted image 20260901220806.png]]


两栏自适应（底层）
BFC 有条规矩：==我不跟 float 元素抢地盘==。
左边 float 一个侧栏占一块，右边内容区如果是 BFC，浏览器排布时就自动让开左边那块，不会被侧栏压住。

```
给右边的内容区上 BFC，不是给 float 的侧栏上。

<div class="sidebar">侧栏</div>   <!-- float: left; width: 200px -->
<div class="content">内容</div>    <!-- 给它上 BFC -->

没 BFC 时：sidebar float 飘到左边、脱离文档流，content "看不见"它，直接铺满整行，结果被 sidebar 压在下面（重叠）：

┌────────┐┌─────────────────────┐
│sidebar ││content 铺满，被侧栏压住│  ← 重叠了
│ 200px  ││（一半在 sidebar 底下）  │
└────────┘└─────────────────────┘

给 content 加 BFC（overflow:hidden 或 flow-root）：

.content { overflow: hidden; }   /* 触发 BFC */

BFC 规矩"不跟 float 抢地盘"生效 → content 自动让开左边 200px，只占右边剩余：

┌────────┐┌──────────────────┐
│sidebar ││content 自动让开   │  ← 不重叠，两栏
│ 200px  ││（只占右边）        │
└────────┘└──────────────────┘
```


## position ⭐

### 五个值

| 值 | 相对谁 | 脱离文档流吗 | 场景/例子 |
| --- | --- | --- | --- |
| `static` | 默认，按文档流正常排 | 否 | 不定位，几乎不显式写 |
| `relative` | **自己原来的位置** | 否（原位还占着） | 给 absolute 子当参照；微调位置不挤别人 |
| `absolute` | **最近的"已定位"祖先**（非 static） | 是 | 弹层/下拉/角标/tooltip；套路：父 relative + 子 absolute |
| `fixed` | **视口**（浏览器窗口），滚动也不动 | 是 | 悬浮导航、返回顶部、弹窗遮罩 |
| `sticky` | 平时像 relative，滚到阈值后像 fixed 吸住 | 否（原位占着） | 表头/导航/目录吸顶 |

==记法：relative 挪自己不腾位；absolute 找已定位祖先、脱离流；fixed 钉在窗口；sticky 滚到点就吸住。==
![[Pasted image 20260901220829.png]]

### sticky 失效
`sticky` 失效常见原因：① 祖先元素 `overflow` 非 visible；② 没设 `top/bottom/left/right` 之一；③ 父容器高度不够 / 被撑开没有滚动空间。


## 函数

### calc / min / max / clamp
`calc()` 混合单位运算；
`min()/max()` 取最小/最大；
==`clamp(min, val, max)` 做流式字号(默认取val，val比min小取min)。==


## flex vs grid

### flex
一维布局；

容器属性（`justify-content/align-items/flex-direction`）
justify-content是主轴排列方式
align-items是辅助轴元素位置
flex direction决定哪个是主轴

项目属性（`flex-grow/flex-shrink/flex-basis/order`）；
grow是  如果这行没占满的伸展比例
shrink是 如果这行太多了的缩减比例
basis是 初始分配长度
`flex: 1` 等价于 `1 1 0%`


flex 的渲染，本质是浏览器渲染引擎按 CSS Flexbox 规范跑的一套"算尺寸"算法。核心就一件事：先给每个项目定起跑线，再按比例分多余/缩不足。

完整分 6 步：

① 定方向：看 flex-direction，确定主轴（横/竖）和交叉轴。

② 每个项目拿"基准尺寸"：flex-basis 就是起跑线（没写就看 width/内容）。

③ 算空间够不够：容器宽 − 所有项目 basis 之和 = 剩余空间（正=有空余，负=溢出）。

④ grow 分 / shrink 缩：
- 空余 → 按 flex-grow 比例分给各项目
- 溢出 → 按 flex-shrink 比例从各项目扣（且不会缩到比内容最小宽度还小）
- 这一步结束，每个项目的主轴最终尺寸就定了

⑤ 交叉轴对齐：主轴定完，按 align-items/align-self 排交叉轴（stretch/center…），定每个项目的高度。

⑥ 分剩下的空白：justify-content 把主轴方向没占满的空位怎么摆（start/center/space-between）。


### grid
二维布局；`grid-template-columns`、`fr` 单位、`gap`、`grid-area`；与 flex 区别。


### 典型布局
圣杯/双飞翼、等高布局、瀑布流各自用什么实现。

---

圣杯/双飞翼 —— 就一句话：三栏 + 中间自适应。

/* flex 版（现代主流） */
.wrap { display: flex; }
.left { width: 200px; } .right { width: 200px; }
.main { flex: 1; }        /* 中间自动撑满 */

（经典面试会问 float + 负 margin 的老写法，圣杯用父 padding、双飞翼用中间内层 margin——但现在实际都写 flex/grid 了。）

等高布局 —— flex 的 align-items: stretch（默认值）直接让几列拉一样高，不用额外做。

瀑布流 —— 一句 columns 搞定：

.list { column-count: 3; column-gap: 16px; }

卡片会自动从上往下、错落填进三列。想要更精细（图片占位不跳动）
再用 JS 库（Masonry 类）。

==记法：三栏用 flex/grid，等高是 flex 默认，瀑布流 columns。==




## css实现响应式

### rem / em / vw / vh / % 等长度单位

| 单位 | 相对谁 | 一句话 |
| --- | --- | --- |
| `px` | 绝对（近似物理点） | 写死不变 |
| `%` | **父元素**（宽对宽、高对高） | 随父 |
| `em` | **当前元素**的 font-size | 随字体，会逐层叠加 |
| `rem` | **根元素 html** 的 font-size | 全局统一缩放 |
| `vw` | 视口宽度的 1% | 屏幕宽 |
| `vh` | 视口高度的 1% | 屏幕高 |
| `vmin` | 视口宽高中**较小**那个的 1% | 取小边 |
| `vmax` | 视口宽高中**较大**那个的 1% | 取大边 |

==rem 做移动端适配原理==：`html` 的 `font-size` 随屏宽动态变，页面尺寸全用 rem → 屏宽变，rem 跟着变，整体等比缩放。

```js
// 设计稿 375，约定 1rem = 37.5px
document.documentElement.style.fontSize = window.innerWidth / 10 + 'px';
// 屏宽 375 → 1rem=37.5px；屏宽 750 → 1rem=75px，元素自动放大
```

### clamp 流式
`font-size: clamp(16px, 2vw, 24px)` 随视口变化。

### @media 媒体查询 ⭐

语法：条件满足时才生效。
```css
@media (max-width: 768px) { .box { color: red; } }
```

两种策略：
| 策略 | 写法 | 思路 |
| --- | --- | --- |
| desktop-first | `max-width` | 先写桌面，小屏逐步覆盖 |
| mobile-first | `min-width` | 先写移动基础，大屏增强（现代推荐） |

例子（mobile-first）：
```css
.nav { flex-direction: column; }   /* 手机默认竖排 */
@media (min-width: 768px) {
  .nav { flex-direction: row; }    /* ≥768px 横排 */
}
```

常用条件：`width/min-width/max-width`、`orientation`（横竖屏）、`hover`（能不能悬停）、`prefers-color-scheme`（深色）、`prefers-reduced-motion`（减少动效）。

### @container 容器查询 ⭐

==@media 看「屏幕多宽」，@container 看「组件所在容器多宽」。==

```css
.card-wrap { container-type: inline-size; }  /* 第①步 */
@container (min-width: 400px) {              /* 第②步 */
  .card { display: flex; }                   /* 第③步 */
}

- 第①步：告诉浏览器——".card-wrap 这个盒子我标记成容器，以后它里面的子孙元素，可以根据这个盒子的宽度来自动换样式"。（不写这行，@container 不生效）
- 第②步：@container (min-width: 400px) = "当上面那个容器宽度 ≥ 400px 时"。
- 第③步：才让里面的 .card 横排。

```

同一个卡片组件：放主内容区（宽 600px）→ 横向；放侧边栏（宽 240px）→ 纵向。**不看屏幕，只看自己被塞进多大的容器。**

| | 看什么 | 场景 |
| --- | --- | --- |
| `@media` | 屏幕（视口） | 页面级 |
| `@container` | 组件自己的容器 | 组件级 |

### 其他长度单位

- `dvh / svh / lvh`：解决移动端地址栏导致 `100vh` 不准。`svh` 最小可视高、`lvh` 最大、`dvh` 动态。全屏弹层/首屏用 `dvh`。
- `ex / ch`：排版单位。`ex` ≈ 小写 x 高度；`ch` ≈ 数字 0 的宽度（常用于控制输入框能放几个字符）。
- `rpx`：小程序单位，750rpx = 屏幕宽（非 CSS 标准，面试小程序岗会问）。

### 移动端适配
viewport、rem 方案（flexible）、postcss-px-to-viewport；逻辑属性 `inline-size` 适配 RTL。


## ==CSS 预处理（Sass / SCSS）

> 先纠正混用：下面 `@extend`、`@mixin`、`%` 占位符都是 **Sass/SCSS** 的语法，不是 Less——Less 没有 `%` 占位符，mixin 写法也不一样。两者是不同工具，语法像但不对等。这里统一用 SCSS 写，Less 的差异在「变量」和「Mixin」顺手标注。

### 总体介绍 & 语法

**一句话**：给 CSS 加「编程能力」（变量、嵌套、函数、继承），写的时候高级，编译成纯 CSS 给浏览器。

```scss
// 这是 SCSS，浏览器不认识
$brand: #1677ff;
.btn { background: $brand; }

// 编译后变成浏览器认识的纯 CSS
.btn { background: #1677ff; }
```

### 变量

**是什么**：把值存起来复用，改一处全局生效。

```scss
$color: #333;   // SCSS 用 $   （Less 用 @color）
$gap: 12px;

.card {
  color: $color;
  padding: $gap;
}
```

### 嵌套

**是什么**：允许选择器往里写子选择器，不用重复写父类。

```scss
.card {
  width: 200px;
  .title {
    font-size: 18px;
  }
}
```

编译后：

```css
.card { width: 200px; }
.card .title { font-size: 18px; }
```

⚠️ 坑：==别嵌套太深，最多 2–3 层==。套 5 层就出来 `.a .b .c .d .e` 这种超长选择器，难覆盖。

### `&` 父选择器占位符

**是什么**：`&` 直接替换成外层选择器名字，**不加空格**。

- 不加 `&`：`.btn :hover`（后代选择器，`hover` 是 `.btn` 的子元素）
- 加 `&`：`.btn:hover`（同一个元素）

==&:hover==
```scss
.btn {
  color: black;
  &:hover {//btn自身被hover
    color: blue;
  }
  &-big {
    font-size: 20px;
  }
  :hover { //btn子元素被hover时
    color: red;
  }
}
```

编译后：

```css
.btn { color: black; }
.btn:hover { color: blue; }
.btn-big { font-size: 20px; }
.btn :hover{ color: red; }
```

速记：==普通嵌套自动加空格（父子）；`&` 直接贴一起（伪类、修饰类）。==

### Mixin（样式函数）

**是什么**：可复用的样式块，能带参数。

```scss
// 定义：用 @mixin
@mixin rounded($r: 4px) {
  border-radius: $r;
}

// 使用：用 @include
.btn { @include rounded(8px); }
.tag { @include rounded(); }   // 不传参，用默认 4px
```

> Less 对应写法是 `.rounded(@r: 4px) { ... }` 然后 `.btn { .rounded(8px); }`，没有 `@mixin`/`@include`。

### `@extend` 继承

**是什么**：把别的选择器名字拿过来跟自己**放同一组**，共用同一条 CSS（不是复制代码）。

```scss
.base-text {
  line-height: 1.5;
  color: #333;
}
.p-text {
  @extend .base-text;
  font-size: 14px;
}
```

编译后：

```css
.base-text, .p-text {
  line-height: 1.5;
  color: #333;
}
.p-text {
  font-size: 14px;
}
```

⚠️ 坑：
1. 不能传参（跟 mixin 不一样）
2. 会到处改动选择器列表，样式关系乱了难排查。实际项目很少用 `@extend`，==优先 mixin==。

### 占位符 `%`

**是什么**：专门给 `@extend` 用的模板，**自己不会被编译输出**——只有当别人 `@extend` 它，才会把样式并进去。

```scss
%btn-base {
  padding: 8px 16px;
  border-radius: 4px;
}

.primary-btn { @extend %btn-base; background: #1677ff; }
.danger-btn  { @extend %btn-base; background: #f5222d; }
```

编译后——注意 `%btn-base` 本身**消失了**：

```css
.primary-btn, .danger-btn {
  padding: 8px 16px;
  border-radius: 4px;
}
.primary-btn { background: #1677ff; }
.danger-btn  { background: #f5222d; }
```

**和普通类的区别**：`@extend .base-text` 会连 `.base-text` 这个类一起输出；`@extend %btn-base` 只输出用它的选择器，不留垃圾模板。

### 函数与运算

```scss
$brand: #1677ff;

.btn {
  background: darken($brand, 10%);        // 变暗 10%
  border: 1px solid lighten($brand, 20%); // 变亮 20%
  padding: 4px * 2;       // 直接算
  width: (100% / 3);      // 除法要加括号
}
```

拆分文件：`@use './variables';`（SCSS 新版）或 `@import './variables';`。

### 一句话速记

- **变量**：`$` 存值（Less 是 `@`）
- **嵌套**：往里写，自动加空格
- **`&`**：父选择器别名，贴一起
- **mixin**：`@mixin` + `@include`，复制代码，可传参
- **extend**：`@extend`，合并选择器，共用代码，不可传参
- **占位符 `%`**：只给 extend 用的模板，自己不输出


## Tailwind CSS

### 原子化理念
用大量工具类（`flex p-4 text-sm`）直接组合样式，不写语义化 class；与 BEM/组件化的区别。


### 常用工具类
布局 `flex/grid`、间距 `p-*/m-*`、颜色 `bg-*/text-*`、排版 `text-*/font-*`、边框圆角阴影。


### 响应式前缀与变体
`sm:/md:/lg:/xl:` 前缀做断点；`hover:`/`focus:`/`dark:` 状态变体。


### 自定义 theme
`tailwind.config` 里 `theme.extend` 自定义颜色/间距/断点；`@apply` 抽取复用。


### 优缺点
优点（快、一致、按需打包）；缺点（HTML 冗长、类名难读、学习成本、团队约定）。


## 主题切换

### 方案（一句话）
颜色等设计值抽成 CSS 变量，切换根元素的 `data-theme`，用属性选择器换变量的值。

```css
:root { --bg: #fff; --text: #333; }
[data-theme="dark"] { --bg: #1a1a1a; --text: #eee; }

body { background: var(--bg); color: var(--text); }
```

```js
// 切主题就改根元素一个属性
document.documentElement.setAttribute('data-theme', 'dark');
```

要点：
- 跟随系统：`@media (prefers-color-scheme: dark) { :root { ... } }` 自动适配
- 持久化：切完存 localStorage，刷新读回来
- 现代写法：`light-dark(#fff, #1a1a1a)` 一行同时写深浅两色

### Sass / Less / Tailwind 实现

==底层认知：Sass/Less 变量是「编译期常量」，编译完就写死进 CSS，没法运行时切换。所以真正切主题还得靠 CSS 变量；Sass/Less 的作用是「帮你批量生成这些 CSS 变量」。==

Sass（map + @each 批量生成主题变量）：
```scss
$themes: (
  light: (bg: #fff, text: #333),
  dark: (bg: #1a1a1a, text: #eee),
);

@each $name, $map in $themes {
  @if $name == light {
    :root { @each $k, $v in $map { --#{$k}: #{$v}; } }
  } @else {
    [data-theme="#{$name}"] { @each $k, $v in $map { --#{$k}: #{$v}; } }
  }
}
```
Less 思路一样（`@` 变量 + 循环），不重复。

Tailwind（`dark:` 变体）：
```js
// tailwind.config
module.exports = { darkMode: 'class' };  // 'class' 手动切 / 'media' 跟系统
```
```html
<div class="bg-white text-gray-900 dark:bg-black dark:text-white">
```
class 策略：`<html class="dark">` 一加，所有 `dark:` 前缀的类自动生效。




### ==主题色的两层：预设 vs 自定义

#### 第一层：主题色（浅/深预设）

一句话：主题色 = 一套预设好的颜色方案，最典型就是「浅色 / 深色」两套，用户点一下切换。

本质：设计师先把整套颜色（背景、文字、主色、边框）定义成变量，切换主题 = 换一组值。组件里永远只写 `var(--bg)`，不写死 `#ffffff`。

```css
/* 默认浅色 */
:root {
  --bg: #fff;
  --text: #111;
  --primary: #2563eb;
}

/* 切深色：往 <html> 上挂个属性就完事 */
:root[data-theme="dark"] {
  --bg: #111;
  --text: #eee;
  --primary: #60a5fa;
}

/* 组件只认变量，永远不用改 */
.card { background: var(--bg); color: var(--text); }
```

```js
// 切换就是加/减一个属性
document.documentElement.dataset.theme = 'dark';
localStorage.setItem('theme', 'dark'); // 记住选择
```

各框架实际落点：

- **Tailwind + shadcn/ui**：色板拆成 HSL 变量，`dark:` 前缀自动切，靠系统自动跟随：

```css
@import "tailwindcss";
:root { --background: 0 0% 100%; }   /* 浅色 */
@media (prefers-color-scheme: dark) {
  :root { --background: 0 0% 9%; }   /* 系统深色自动生效 */
}
```

- **Next.js**：用 `next-themes` 库，本质同上，帮你处理「防闪白 + localStorage + 跟随系统」。
- **CSS 原生**：根元素加一行 `color-scheme: light dark;`，浏览器连滚动条、表单控件都自动换。

关键就一个：==颜色不写死在组件里，写进 CSS 变量。主题切换 = 换变量，代码零改动。==

#### 第二层：用户自定义主题色（种子色派生）

一句话：用户不满足「浅色/深色」两套，要自己选一个品牌色（点「紫色」，整个 App 主色都变紫）。

难点：用户只给一个颜色，但实际要用一串——主色、hover、点击态、浅背景、描边……这串从哪来？—— 这就是「派生」。

现代做法：CSS 原生 `color-mix()`，几乎不用写 JS。

```css
:root {
  --primary: #2563eb;   /* 用户选的种子色，只有这一个！ */
}

.btn { background: var(--primary); }
.btn:hover {
  background: color-mix(in oklch, var(--primary) 80%, white);  /* 混 20% 白 */
}
.btn:active {
  background: color-mix(in oklch, var(--primary) 85%, black);
}
.tag {
  background: color-mix(in oklch, var(--primary) 12%, transparent); /* 浅底 */
  color: var(--primary);
}
```

```js
// 用户选完色，只改 --primary 一个值，所有派生色全自动跟着变
document.documentElement.style.setProperty('--primary', '#7c3aed');
localStorage.setItem('primary', '#7c3aed');
```

各框架的自定义主题色实现：

| 框架/库 | 怎么让用户自定义主色 | 底层 |
| --- | --- | --- |
| Ant Design v5 | `<ConfigProvider theme={{ token: { colorPrimary: '#7c3aed' } }}>`，内部 `generate()` 自动算 hover/active/bg 一整串 | JS 算法派生 |
| Element Plus | 覆盖 `--el-color-primary`，配套 `color-mix` 派生 light-3~light-9 | CSS 变量 + color-mix |
| Naive UI / Vant | 提供 `themeOverrides`，传一个主色自动生成整套 | JS 算法派生 |
| Chakra UI v3 | `createSystem` + token，运行时覆盖主色 | 设计 token |
| Material 3 | 用户给种子色 → HCT 色空间生成 0~100 整条色阶，明暗对比度自动达标 | 色空间科学 |

从 hex 到能派生，色空间升级路线：hex（没法派生）→ HSL（够用）→ ==OKLCH（感知均匀、混色自然，现在主流）== → HCT（Material 3 专用）。

#### 一句话总结两层

- 主题色：把颜色做成变量，切「浅色/深色」就是换一组值。
- 自定义主题色：用户给一个种子色，靠 `color-mix()`（或框架算法）自动派生一整串变体，换种子色全自动跟着变。

==记忆锚点：颜色永远以「一个可派生的变量」存在，绝不写死成 hex 死在组件里。前者靠 data-theme 换组，后者靠 color-mix 派生。==

#### token 体系：三层结构

```css
/* 第 1 层 primitive 原始色板层：一大批 xxx-1 ~ xxx-10 */
--red-1 / --red-5 / --red-10
--blue-1 ~ --blue-10

/* 第 2 层 semantic 语义层：组件真正用到的，指向色板某档 */
--color-primary: var(--blue-6);
--color-success: var(--green-6);
--color-bg: var(--black-1);

/* 第 3 层 组件：只认语义名 */
.btn { background: var(--color-primary); }
```

==实际上大多数团队只做两层==：primitive + semantic，组件直接吃语义 token，没有第三层「组件 token」。

```css
--blue-500: #1677ff;                       /* primitive */
--color-primary: var(--blue-500);          /* semantic */
.btn { background: var(--color-primary); } /* 组件直接用 */
```

> 层数不是规范：primitive → semantic → component 是完整三档，两层是常态。第三层只有大设计系统、需要组件级独立调色时才上。




## 多行文本兼容

### 单行截断
`white-space: nowrap + overflow: hidden + text-overflow: ellipsis`。


### 多行截断
`display: -webkit-box + -webkit-line-clamp + -webkit-box-orient: vertical`。


### 长单词换行
`word-break: break-word` / `overflow-wrap: anywhere`。




## CSS 新特性速览（2024–2026，常考"知道吗"）

### CSS Nesting 原生嵌套
原生 `&` 嵌套，替代 Less/Sass 的嵌套需求。


### @layer 层叠层
`@layer base, components, utilities` 显式控制优先级，摆脱 `!important` 和特异性 Hacks。


### aspect-ratio
`aspect-ratio: 16/9` 保持宽高比，替代 padding hack。


### 深色模式 light-dark() 与 color-mix()
`light-dark()` 随系统深浅色切换；`color-mix(in srgb, red 50%, blue)` 混色。


### 滚动驱动动画
`animation-timeline: scroll()` 把动画绑定滚动进度，不占主线程。


### @starting-style 入场动画
`display: none → block` 的原生过渡动画（`@starting-style` + `transition allow-discrete`）。


### field-sizing 与锚点定位
`field-sizing: content` 让 textarea 自动撑高；Anchor Positioning 原生替代 Popper/Floating UI。


### prefers-reduced-motion
根据用户"减少动效"偏好降级动画，无障碍友好。


