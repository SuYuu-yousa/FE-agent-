# css基础常考点

## 选择器

二、选择器一定会考。你要掌握：元素选择器、类选择器、id 选择器、后代、子代、相邻兄弟、通用兄弟、属性选择器、伪类、伪元素。高频示例：`div`、`.box`、`#app`、`ul li`、`ul > li`、`a + span`、`a ~ span`、`input[type="text"]`、`:hover`、`:focus`、`:nth-child(n)`、`::before`、`::after`。现在实际业务中类选择器最常见，id 选择器很少作为样式主方案。你还要知道 `:not()`、`:first-child`、`:last-child`、`:nth-of-type()` 这些也很常考。

## 优先级

1. `!important`（最高，尽量少用）
2. **行内样式**：`style=""` → `1-0-0-0`
3. **ID 选择器**：`#id` → `0-1-0-0`
4. **类/伪类/属性选择器**：`.class`、`:hover`、`[type="text"]` → `0-0-1-0`
5. **元素/伪元素选择器**：`div`、`::before` → `0-0-0-1`
6. **通配符/继承**：`*、>、+、~、空格` → `0-0-0-0`

==多个选择器优先级相同怎么办，答“看后写的覆盖先写的”==

恋爱类圆通

（内联 ID 类 元素选择 通配符）




## 盒模型

content、padding、border、margin、`box-sizing`。

标准盒模型下，`width/height` 只算 content；

怪异盒模型/现在通过 `box-sizing: border-box` 实现的效果是 `width/height` 包含 padding 和 border。



## 居中

1. margin: auto

2. position: absolute;left: 50%;top: 50%;transform: translate(-50%, -50%);

3.  grid: `place-items: center`。

4. flex+ `justify-content: center`。`align-items: center`

5. 单行文本 `line-height = height`、`text-align: center`

   

## 伪类

:hover :active :focus :visited :first-child :nth-child(n)



## position



## 函数

```
calc()`、`min()`、`max()`、`clamp()
```



## flex

flex:1 等价于 110

```
flex-grow: 1;(侵占权重)
flex-shrink: 1;（割让权重）
flex-basis: 0%;（初始分配假设）
```



## grid



## 主题切换

我会把颜色、字体、圆角、阴影、间距等抽成 design tokens，通过根节点挂主题标识或切换 data-theme/class，然后用 CSS变量驱动页面样式变化。

核心就 3 个语法点：

1. **`data-theme="dark"` 是自定义属性（HTML 属性）**

```
<html data-theme="dark">
```

只是给根元素打个“标记”，方便 CSS 选择它。

2. **CSS 变量：`--xxx` 定义，`var(--xxx)` 使用**

```
:root { --color-bg: #fff; }      /* 定义变量（全局默认） */
.box { background: var(--color-bg); }  /* 使用变量 */
```

3. **属性选择器：`[data-theme="dark"]`**  
    意思是“选中带这个属性的元素”（这里是 html），在它下面变量会被覆盖：

```
[data-theme="dark"] { --color-bg: #111; }
```

所以流程是：

- `:root` 先给一套默认变量（light）
- 当 html 变成 `data-theme="dark"`，CSS 命中 `[data-theme="dark"]`，把变量值改掉
- 组件都用 `var(--color-bg)`，不用改组件 CSS，只是变量换了

JS 切换就一句：

```
document.documentElement.dataset.theme = 'dark' // 或 'light'
```


