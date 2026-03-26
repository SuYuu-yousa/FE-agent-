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

==多个选择器优先级相同怎么办，答“看后写的覆盖先写的”==，

## 盒模型

盒模型是重中之重。关键词：content、padding、border、margin、`box-sizing`。标准盒模型下，`width/height` 只算 content；IE 盒模型/现在通过 `box-sizing: border-box` 实现的效果是 `width/height` 包含 padding 和 border。现代项目几乎都会全局设 `box-sizing: border-box`、

## 居中

1. margin: auto
2. position: absolute;left: 50%;top: 50%;transform: translate(-50%, -50%);
3.  grid: `place-items: center`。
4. flex+ `justify-content: center`。`align-items: center`、
5. 单行文本 `line-height = height`、`text-align: center`、



## position



## flex



## grid



