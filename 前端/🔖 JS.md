# JavaScript 核心（面试版）
JS 是面试最容易被深挖的一门

## 一、数据

### 1. 原始类型 vs 引用类型 & 深浅拷贝  & 栈与堆
> 要写：7 种原始类型（string/number/boolean/null/undefined/symbol/bigint）+ Object；栈 vs 堆；深浅拷贝（含 `structuredClone`）。


1. **原始类型（Primitives）**：七个， string/number/boolean/null/undefined/symbol/bigint
2. **对象类型（Objects）**：`Object` 及其所有子类。（array  function）


JS底层栈存指针和原始类型变量  堆存map(对象)
```
const obj ={a:1, b:{c:2} }
- 栈：变量名 obj  → 存放堆地址  0x100
- 堆 0x100 ： {a:1, b: 0x200} ，其中 b 又是一个指向堆 0x200 的地址 ​ 
- 堆 0x200 ： {c:2}
  
let x =obj 
- 栈：变量名 obj  → 存放堆地址  0x100
- 栈：变量名 x  → 存放堆地址  0x100
- 堆 0x100 ： {a:1, b: 0x200} ，其中 b 又是一个指向堆 0x200 的地址 ​ 
- 堆 0x200 ： {c:2} 
  
//浅  
let Shadow = {...obj}  
- 栈：变量名 obj  → 存放堆地址  0x100
- 栈：变量名 shadow  → 存放堆地址  0x300
- 堆 0x100 ： {a:1, b: 0x200} ，其中 b 又是一个指向堆 0x200 的地址 ​ 
- 堆 0x200 ： {c:2} 
- 堆 0x300 ： {a:1, b: 0x200} ，其中 b 又是一个指向堆 0x200 的地址 ​ 

//深
let deep = lodash.deepClone(obj)
- 栈：变量名 obj  → 存放堆地址  0x100
- 栈：变量名 deep → 存放堆地址 0x300
- 堆 0x100 ： {a:1, b: 0x200} ，其中 b 又是一个指向堆 0x200 的地址 ​ 
- 堆 0x200 ： {c:2} 
- 堆 0x300 ： {a:1, b: 0x400} ，其中 b 又是一个指向堆 0x400 的地址 ​ 
- 堆 0x400 ： {c:2}  ​ 

```

等号赋值： 只操作栈，不操作堆，把一个在栈地址的值给另一个栈地址
浅拷贝：操作一层栈，遍历map复制一层堆
深拷贝：操作一层栈和若干层堆


浅拷贝两种方法： let a ={...b}   let a = object.assign({},b)   [...a]
深拷贝常用lodash 库deepclone
还可以json.parse   structuredClone 手写 前两者没法包含函数

```
实际例子（等号只动栈；浅拷贝动一层栈+一层堆；深拷贝动一层栈+若干层堆）：
const user = { name: '张三', info: { age: 18 } }
const a = user; a.name = '李四'        // user.name 变'李四' ❌ 同一个对象
const b = { ...user }; b.info.age = 99  // user.info.age 变99 ❌ 深层还连着
const c = structuredClone(user); c.info.age = 99  // user.info.age 还是18 ✅ 全断开
```

```
区分Object.assign(A, B)    Object.assign({}, B) 
// 写法1：把B合并到A，A原地被修改 
let A = {x:1} let B = {y:2} Object.assign(A, B) 
// A现在变成 {x:1,y:2}，A被改了！   
// 写法2：做浅拷贝，不污染原对象 ✅常用 let A = {x:1} let B = {y:2} 
let A = Object.assign({},B) // {}是target空对象，B是源；返回全新对象 
// 浅拷贝
```
### 2. 类型判断
> 要写：`typeof`（`typeof null` 是 `object` 的坑）/ `instanceof` / `Object.prototype.toString` 三者区别与适用场景。

生产直接用 Lodash（大厂项目基本都装了），别自己拼原生 API：

```js
_.isNumber(1)          // true
_.isString('a')        // true
_.isArray([1, 2])      // true
_.isPlainObject({})    // true —— 只认纯对象，排除数组/函数
_.isNil(null)          // true —— 同时判 null 和 undefined
_.isNil(undefined)     // true
_.isEmpty({})          // true —— 空对象/空数组/空串都算空
_.isEmpty([1])         // false
```

记忆锚点：判类型用 `isXxx`，判"是不是空"用 `isEmpty`，判 `null`/`undefined` 用 `isNil`。

底层原理（面试考，生产基本不写）：`typeof`（坑：`typeof null === 'object'`）/ `instanceof`（沿原型链找）/ `Object.prototype.toString`（万能但啰嗦）。


### 3. 类型转换：隐式转换与 ToPrimitive

#### 3.1. 什么时候需要类型转换

隐式转换 = 运算符需要某种类型 → JS 自动把操作数转成它要的类型。也就是你在没写 `Number(x)`/`String(x)` 的情况下，JS 自己把类型变了。

常见触发场景：`+`、`-` `*` `/` `%`、`>` `<`、`==`，以及 `if()` / `!` / `&&` 等需要布尔值的地方。

#### 3.2. 类型转换四大函数

JS 引擎内部有 4 个"抽象操作"，所有运算符都靠它们：

| 抽象操作                       | 作用                        |
| -------------------------- | ------------------------- |
| `ToPrimitive(input, hint)` | 把任何值变成原始类型：原始类型原样返回，对象才降级 |
| `ToNumber(arg)`            | 变成数字                      |
| `ToString(arg)`            | 变成字符串                     |
| `ToBoolean(arg)`           | 判断真假                      |
|                            |                           |
好的，按照您给的列表格式，把 **`ToPrimitive` 的完整流程**整理如下：

---

- ==**`ToPrimitive(input, hint)`**：将任意值转换为原始值（核心算法）。==
    - ==0：若 `input` 已是原始类型，**直接返回**，流程结束。==
    - ==1：若 `input[Symbol.toPrimitive]` 存在，则调用它并传入 `hint`。==
        - ==若返回原始值 → 直接返回该值。==
        - ==若返回对象 → 抛出 `TypeError`。==
    - ==2：无 `Symbol.toPrimitive` 时）：==
        - ==`hint = "string"` **或** `Date` 对象且 `hint = "default"`：==
            - ==先调用 `toString()`，若返回原始值则返回；==
            - ==否则调用 `valueOf()`，若返回原始值则返回；==
            - ==否则抛出 `TypeError`。==
        - ==`hint = "number"` **或**普通对象且 `hint = "default"`：==
            - ==先调用 `valueOf()`，若返回原始值则返回；==
            - ==否则调用 `toString()`，若返回原始值则返回；==
            - ==否则抛出 `TypeError`。==
    - ==**默认 `hint`**：若未提供 `hint`，普通对象默认当作 `"number"`，`Date` 默认当作 `"string"`。==
    - ==**终止条件**：只要任意一步返回原始值，立即停止并返回；全部失败则报错。==


**ToNumber**：`'5'`→5，`''`→0，`'abc'`→NaN，`true`/`false`→1/0，`null`→0，`undefined`→NaN。  
**ToString**：5→`'5'`，NaN→`'NaN'`，`true`/`false`→`'true'`/`'false'`，`null`/`undefined`→`'null'`/`'undefined'`。  
**ToBoolean**：假值仅 `false`、`0`、`-0`、`0n`、`''`、`null`、`undefined`、`NaN`，其余全真。


- **`toString()`**：返回**字符串**。
  
    - 普通对象 `{}` → `"[object Object]"`
    - 数组 `[1,2]` → `"1,2"`
    - 日期 `new Date()` → `"2026-09-03T..."`（日期字符串）
    
- **`valueOf()`**：返回**原始值**，若没有则返回**对象本身**。
  
    - 普通对象 `{}` → `{}`（自身，非原始值）
    - 数字/布尔包装类 `new Number(1)` → `1`（原始值）
    - 日期 `new Date()` → `175...`（时间戳数字）

#### 3.3. 运算符 hint 表
hint 由运算符决定

| 运算符 | hint | 顺序 |
|---|---|---|
| `+` 加号 | default | 先 valueOf 后 toString |
| `==` 双等号 | default | 先 valueOf 后 toString |
| `-` `*` `/` `%` | number | 先 valueOf 后 toString |
| `>` `<` 比较 | number | 先 valueOf 后 toString |
| `String()` / `` `${x}` `` | string | 先 toString 后 valueOf |

> 规律：绝大多数（`+`、`==`、算术、比较）都是先 valueOf 后 toString；只有明确要字符串（`String()`、模板字符串）才反过来。

#### 3.4. 运算符操作算法

**`+` 号的底层算法**：

```js
function 加法(a, b):
  lprim = ToPrimitive(a)      // 原始类型原样返回，对象降级
  rprim = ToPrimitive(b)

  if (lprim 是字符串 或 rprim 是字符串):
      return ToString(lprim) + ToString(rprim)   // 有字符串 → 拼接
  else:
      return ToNumber(lprim) + ToNumber(rprim)   // 没字符串 → 数字相加
```

**`<` `>` 比较的底层算法**：

```js
function 比较(a, b):
  lprim = ToPrimitive(a, hint='number')
  rprim = ToPrimitive(b, hint='number')

  if (lprim 是字符串 且 rprim 是字符串):
      按字典序比较（'10' < '9' 为 true，逐字符比）
  else:
      return ToNumber(lprim) < ToNumber(rprim)
```

#### 5. 举例

**`'10' + 5` → `'105'`**：

```js
ToPrimitive('10') → '10' 是原始类型 → 原样返回 '10'
ToPrimitive(5)    → 5 是原始类型 → 原样返回 5
结果里有字符串 '10' → 拼接：ToString('10') + ToString(5) = '105'
```

**`obj + 1` → `'[object Object]1'`**：

```js
const obj = { name: '张三' }
ToPrimitive(obj) → 对象 → 先 valueOf（返回自己，还是对象，继续下一步）→ 再 toString → '[object Object]'
结果里有字符串 → 拼接：'[object Object]' + '1' = '[object Object]1'
```

**`[1, 2] + 1` → `'1,21'`**：

```js
ToPrimitive([1,2]) → 数组 toString 被重写成 join(',') → '1,2'
拼接：'1,2' + '1' = '1,21'
```

> 大厂规范：正因 `+` 和 `==` 会悄悄转类型，生产强制 `===`、要运算显式 `Number()`。


### 4. `==` vs `===`
> 要写：抽象相等算法；`==` 会类型转换，`===` 不会；为什么推荐 `===`。
> 
1. 类型相同吗？ → 是 → 直接比。
2. 是 `null` 和 `undefined` 互比吗？ → 是 → `true`。
3. 有布尔值吗？ → 是 → 布尔转数字，回到开头。
4. 是数字和字符串比吗？ → 是 → 字符串转数字，回到开头。
5. 有对象吗？ → 是 → 对象转原始，回到开头。
6. 都不是 → `false`。


### 5. 经典转换题
> 要写：解释 `[] + []`、`[] + {}`、`{} + []`、`true + true`、`1 + '1'`、`null == undefined` 的结果和原因。

[]+[]  ""
[]+{}  "{object,object}"
`{} + []`  0 {}被当作空语句  实际执行+[]   (正[])   正的规则是tonumber(topri)  同number("")  0
2
11
true



## 二、闭包 ⭐

### 1. 从作用域讲起

JS 是静态（词法）作用域，函数在哪定义就记哪的变量。

作用域分三种：

- 全局作用域：最外层，处处可访问
- 函数作用域：`function` 里，`var` 属于这一层
- 块级作用域：`{ }` 里，`let`/`const` 属于这一层

**静态（词法）作用域**：函数在哪里**定义**，就记住哪里的变量；不是在哪里**调用**。

```js
let name = '张三';
function fn() {
  console.log(name);   // 定义在全局，就认全局的 name
}
function outer() {
  let name = '李四';
  fn();                // 调用时，fn 还是认它定义处的 '张三'
}
outer();               // '张三'
```

### 2. 那闭包是什么
> 要写：函数 + 其外层词法作用域的组合；内层函数引用外层变量，即使外层函数执行完变量也不被回收。

一句话：**内层函数引用了外层函数的变量，这个"内层函数 + 它记住的外层变量"就叫闭包。**

```js
function outer() {
  const count = 0;          // 外层变量
  return function inner() {
    return ++count;         // inner 引用了 count
  };
}
const add = outer();        // outer 执行完，但 count 没被回收
add();  // 1
add();  // 2  ← count 一直活着

add = null //随add销毁，count才被回收
```

上面一系列，发生了什么？

==编译阶段，编译引擎会做一件事：==
	==解析到 `function inner() { ... }` 时，发现 inner 函数体里引用了外部变量count。==
	==引擎就会把**当前外层的“词法环境记录”（可以理解为变量的快照）**，打包塞进 inner 函数的一个内部属性里，这个属性叫 **`[[Environment]]`**。==
	==`inner.[[Environment]] = outer 函数当前的词法环境（里面包含 count）`==
	 

==内存角度：==
- ==当引擎发现 `count` 被内层函数引用时，它**不会**把 `count` 分配到普通的“栈帧”上。==
- ==而是直接在**堆（Heap）里申请一块特殊的内存，叫做 `Context`（上下文对象）**，把 `count` 放在这个 `Context` 里。==
- ==`outer` 的栈帧里只存一个**指针**，指向堆里的这个 `Context`；==
- ==`inner` 的 `[[Environment]]` 也指向同一个 `Context`==

一个字段如果是引擎创建对象时自动塞进去的，JS 代码不能直接读它（所以叫「隐藏」+ 用双层中括号 [[Environment]] 表示它是规范内部字段）
==本质（最重要的）：**本来该随栈帧销毁的变量，因为被内层函数引用，会在堆里长期存活，栈帧只存一个指向堆Context区的指针。**==

所以回来看代码
```js
function outer() {
  let count = 0;          // 外层变量
  return function inner() {
    return ++count;         // inner 引用了 count
  };
}
const add = outer();  // outer 第一次执行，返回了inner函数的地址，给add变量
//outer执行完后，垃圾回收启动，发现add  to inner.[[environment]]可达count，不会回收count

add();  // 正常达到context区的0 ,++返回1
add();  // 2  ← count 一直活着

add = null //随add销毁，count才被回收
```



### 3. 应用场景
> 要写：计数器、防抖节流、私有变量、柯里化、模块模式；手写一个计数器。

**① 防抖 / 节流（生产最高频，lodash 的 debounce/throttle 本质就是闭包）**

```js
function debounce(fn, delay) {
  let timer = null;              // 闭包记住 timer
  return function (...args) {
    clearTimeout(timer);
    timer = setTimeout(() => fn(...args), delay);
  };
}
const search = debounce(api, 500);   // 连续输入只发一次请求


```

==settimeout能启动一个后台线程任务，任务倒计时结束会让最主线程调用函数，clear能通过timer清掉这个后台线程任务，==

**② 私有变量（模块模式）**

```js
function createCounter() {
  let count = 0;                 // 外部访问不到，只能通过返回的方法改
  return {
    add: () => ++count,
    get: () => count,
  };
}
```

**③ 计数器（手写）**

```js
function makeCounter() {
  let n = 0;
  return () => ++n;
}
const c = makeCounter();
c(); // 1  c(); // 2  c(); // 3
```

**④ 柯里化**

```js
const add = a => b => a + b;
const add5 = add(5);
add5(3);   // 8
```

### 4. 内存泄漏
> 要写：闭包会让外层变量常驻内存，滥用会导致泄漏；如何避免（及时置 null、不在大对象上闭包）。

闭包让变量常驻堆里，**只要引用没断就一直在**，滥用会泄漏：

```js
// ❌ 大对象被闭包抓住，永远不放
function leak() {
  const big = new Array(1000000).fill(1);   // 大数组
  return () => big.length;
}
const f = leak();   // f 一直存在 → big 永远占着内存
```

**生产里最常见的三种泄漏**：

```js
// ① 事件监听器没移除
element.addEventListener('click', handler);
// 删了 element 但 handler 还引用它 → 内存收不回

// ② setInterval 没清
let timer = setInterval(() => { /* 引用大对象 */ }, 100);
// 忘了 clearInterval(timer)

// ③ 全局变量
window.cache = hugeObject;   // 永远有引用
```

**怎么避免**：用完**及时置 null**（`big = null`）、**不在大对象上闭包**（只闭包需要的小变量）、移除监听器、`clearInterval`、别滥用全局。

> 一句话：闭包本身不是泄漏，**"一直不释放的引用"才是泄漏**。






## 三、this
---

### 0. 先说本质：`this` 到底是什么？（大纲缺失）
> **`this` 是函数执行时，JS 给每个非箭头函数传的第0号形参。
> 所以定义时定不了，只有在调用时才能确定**（和闭包的词法作用域完全相反）。
> 
```
const obj = { name: 'zhangsan' };
function bar() {
  console.log(this);          // 这里拿到的就是 { name: 'zhangsan' } 本体
  const copy = this;          // 你甚至可以把它赋给其他变量
  copy.name = 'lisi';         // 直接改了原对象
}
bar.call(obj);
```

### 1. 四种绑定规则（补充“优先级 + 大厂判断法”）


| 规则         | 写法                        | this 指向                                    | 大厂高频陷阱                                                         |
| :--------- | :------------------------ | :----------------------------------------- | :------------------------------------------------------------- |
| **默认绑定**   | 直接调用 `fn()`               | 非严格模式：`window` / `global`；严格模式：`undefined` | **严格模式必须写 `'use strict'`，否则全局变量满天飞，排查半天找不到原因**                 |
| **隐式绑定**   | 对象调用 `obj.fn()`           | `obj`                                      | **隐式丢失**：`const fn = obj.fn; fn()` 指向全局——这是 React 类组件事件报错的最大元凶 |
| **显式绑定**   | `call` / `apply` / `bind` | 传入的第一个参数                                   | 传 `null` / `undefined` 会被替换为全局对象（严格模式除外），谨慎使用                  |
| **new 绑定** | `new Fn()`                | 新创建的实例对象                                   | **优先级最高**（可以覆盖前三种）                                             |

> **记忆口诀（优先级从低到高）**：默认 < 隐式 < 显式 < new（new 最大）。

```
function greet(age) {
  console.log(`我叫${this.name}，今年${age}岁`);
}

const obj = { name: '张三' };

// 1. call：立刻执行，当场喊出来
greet.call(obj, 18); // 输出：我叫张三，今年18岁 （函数立刻跑了）

// 2. apply：立刻执行，只是传参用数组
greet.apply(obj, [18]); // 输出：我叫张三，今年18岁 （函数也立刻跑了）

// 3. bind：不执行，只是预制一个“新函数”
const boundGreet = greet.bind(obj); // 这行啥都没打印，函数没跑
boundGreet(18); // 这里才输出：我叫张三，今年18岁 （你手动调用时才跑）
```


### 2. 箭头函数 this（大厂为什么爱用？）
> 箭头函数没有自己的 `this`，它沿袭**外层（定义时）的词法作用域**中的 `this`。

**大厂核心场景**：在 `setTimeout` 或事件监听里，我们**不再写 `const that = this`**，直接用箭头函数保住 `this`。

```javascript
// 大厂老写法（ES5 备胎）
const that = this;
setTimeout(function() { console.log(that.name); }, 1000);

// 大厂新写法（箭头函数，简洁且天然保 this）
setTimeout(() => { console.log(this.name); }, 1000);
```

### 3. 哪些地方要用箭头函数

**核心：凡是你亲自把函数地址交给别人（return React render、定时器、Promise）去调用的，就用箭头函数；凡是 React 框架内部主动通过实例点出来调用的（生命周期），就用普通函数。**

场景一：setTimeout 和 Promise（保 this）
setTimeout / Promise / 网络请求回调 箭头函数 需要拿外层的 Vue/React 实例来 setState。
这两者都是把一个函数地址交给后台线程，如果写普通函数里面用了this，会在传递过程中丢失，箭头函数会去找定义时的this，也就是vm这个对象

```javascript
const vm = {
  name: 'Vue组件',
  data: { count: 0 },
  
  // 模拟组件方法
  fetchData() {
    // 1. 用箭头函数：this 指向外层的 vm
    setTimeout(() => {
      console.log('箭头函数:', this.name); // 输出 'Vue组件'（成功拿到）
    }, 1000);

    // 2. 用普通函数：this 指向 window（裸奔调用）
    setTimeout(function() {
      console.log('普通函数:', this.name); // 输出 undefined 或报错
    }, 1000);

    // 3. Promise 同理
    Promise.resolve().then(() => {
      console.log('Promise箭头:', this.name); // 输出 'Vue组件'
    });
  }
};
vm.fetchData();
```

---

场景二：数组方法（单纯为了简洁）
map/filter/forEach
```javascript
const arr = [1, 2, 3];

// 箭头函数：一目了然，一行返回
const double1 = arr.map(item => item * 2); 
console.log(double1); // [2, 4, 6]

// 普通函数：写法笨重，尤其在链式调用里很难看
const double2 = arr.map(function(item) { 
  return item * 2; 
});
console.log(double2); // [2, 4, 6]
// 结论：这俩输出一样，但箭头函数在大厂代码规范中更受推崇（简洁）
```

---

场景三：React 类组件（大厂经典必考题）
React 类组件方法 箭头函数（类属性） 绑定事件时 React 会裸调，用箭头锁住组件实例。
```jsx
import React from 'react';

class MyComponent extends React.Component {
  state = { count: 0 };

  // ❌ 普通方法：如果直接绑定 onClick，this 是 undefined
  handleClickBad() {
    // 这里打印的 this 是 undefined（React 严格模式下裸调）
    this.setState({ count: this.state.count + 1 }); // 报错！
  }

  // ✅ 方案1：类属性箭头函数（官方推荐写法）
  handleClickGood = () => {
    // 箭头函数锁死了外层的组件实例 this
    this.setState({ count: this.state.count + 1 });
  }

  render() {
    return (
      <div>
        {/* ❌ 错误写法：直接传普通方法，事件触发时 this 丢失 */}
        <button onClick={this.handleClickBad}>报错按钮</button>
        
        {/* ✅ 正确写法1：直接传箭头函数（包裹一层） */}
        <button onClick={() => this.handleClickBad()}>可以，但每次渲染生成新函数</button>
        
        {/* ✅ 正确写法2：传类属性箭头函数（性能最优） */}
        <button onClick={this.handleClickGood}>完美按钮</button>
      </div>
    );
  }
}
```

---

场景四：DOM 事件监听（大厂唯一禁止用箭头函数的地方）
DOM 事件监听 (addEventListener) 普通函数 需要让 this 动态指向被点击的具体 DOM 元素。
```html
<button id="myBtn">点我</button>
```

```javascript
// 给按钮添加点击事件
const btn = document.getElementById('myBtn');

const handler = {
  name: '外部对象',
  
  // 错误方式：使用箭头函数
  bindWrong() {
    btn.addEventListener('click', () => {
      // ❌ 这里的 this 指向 handler 对象（因为定义时锁死了）
      // 拿不到按钮本身，无法操作 DOM 样式
      console.log(this);        // 输出 { name: '外部对象' }
      this.classList.toggle('active'); // 报错：this.classList 是 undefined
    });
  },

  // 正确方式：使用普通函数
  bindRight() {
    btn.addEventListener('click', function() {
      // ✅ 这里的 this 指向 btn 元素（谁调用指向谁）
      console.log(this);        // 输出 <button id="myBtn">...</button>
      this.classList.toggle('active'); // 成功切换按钮样式
    });
  }
};

// 分别调用测试
// handler.bindWrong(); // 点击会报错
// handler.bindRight(); // 点击按钮背景色会变化
```

---



### 5. 大厂必问真实现场：`this` 在生产中的“消费”实例

**场景一：事件监听后取数据（隐式丢失救场）**
```javascript
// 业务代码：点击按钮拿 id
button.addEventListener('click', function() {
  console.log(this.id); // this 指向 button ✅
});

// 但如果先提取出来，this 就丢了
const handler = button.addEventListener.bind(button); 
handler('click', function() { console.log(this.id); }); // 强行 bind 住 button
```

**场景二：Vue 3 / React Hooks 里的 `this` 窒息操作**
> React 函数式组件里没有 `this`，用闭包代替。但**面试官会反问你**：“既然函数式组件没有 `this`，那为什么我们还要学？”  
> 标准回答：**因为在 Vue 2 选项式 API、React Class 组件、以及老代码库维护中，`this` 无处不在**。比如 Vue 2 的 `methods` 里，`this` 指向 Vue 实例，一旦你在 `setTimeout` 里丢了 `this`，页面数据就更新不了。

**场景三：类继承中的 `super` 与 `this`**
```javascript
class Parent { constructor() { this.name = 'parent'; } }
class Child extends Parent {
  constructor() {
    super(); // 必须先调用 super，否则拿不到 this
    console.log(this.name);
  }
}
```


### 6. 终极避坑小结（直接抄进笔记）
1.  **谁调用我，我就指向谁**（隐式绑定口诀）。
2.  **对象里的函数提取出来用，`this` 会丢**（隐式丢失）。
3.  **箭头函数救场 `setTimeout` 和事件监听**（不用写 `that = this`）。
4.  **`bind` 返回新函数不执行，`call/apply` 立即执行**。
5.  **手写 `bind` 必须判断 `new`**，否则面试官直接打 60 分。





## 四、原型与原型链 ⭐

### 1. prototype / __proto__ / constructor
> 要写：三者关系；`__proto__` 指向构造函数的 `prototype`；`prototype.constructor` 指回构造函数。

先点破本质（谁是谁、谁指向谁）：

所有对象都有一个内部字段[[Prototype]]，类型是一个指针，指向另一个对象或者null
我们无法通过obj.[[Prototype]]访问这个字段，所以都以obj.__proto__来代替（一个API函数）
我们写obj.__proto__时，本质上是想取得这个obj的[[Prototype]]字段值 

对于函数而言，函数也是一种对象数据结构，prototype是他自身的一个对象属性
prototype类型是对象，默认值为空对象{consstrcutor: function}

`Foo.prototype` **不是**用来给 `Foo` 自己用的（`Foo` 几乎不访问自己的 `.prototype`）。它的**唯一存在意义**，是作为 `new Foo()` 执行时的**构造参数**，用来**原样写入**新生成实例的 `[[Prototype]]` 指针字段。
F实例的==[/[Prototype ]]=== F的

整理：
- ==`[[Prototype]]` 是「每个对象」身上的一个隐藏字段，类型是指针，指向某个对象的地址==
- ==`__proto__` 不是字段，是挂在 `Object.prototype` 上的 getter/setter，用来读/写上面那个 `[[Prototype]]` 字段==
- ==`prototype` 是「函数对象」身上的一个普通属性，值是一个堆上的普通对象==
- `constructor` 是 `prototype` 对象上的一个属性，指回构造函数，

```js
function Foo() {}
const f = new Foo();
```

```js
f.__proto__ === Foo.prototype          // true —— 都指向 0x200
Foo.prototype.constructor === Foo       // true —— constructor 指回 0x100
```


### 1.5. 原型链查找

为什么 `arr.push()` 能用（生产天天在发生）：`push` 不在 `arr` 自己身上，而在 `Array.prototype` 上。JS 找属性顺序：==先在自己身上找，找不到就顺着 `__proto__` 往上一层找==，这就是「原型链查找」。

对于 `const arr = []` 执行 `arr.push()`：

```

查 arr 自身 → 没有 push
查 arr.__proto__ → 这个值就是 Array.prototype 对象
   （注意：不是查 arr.prototype，因为 arr 没有 prototype 属性）
在 Array.prototype 里找到 push → 返回，结束（Array内部实现有一句Array.prototype.push = function (){...}）
如果 Array.prototype 里也没有（比如查 arr.toString）：
查 arr.__proto__.__proto__ → 即 Array.prototype.__proto__，也就是 Object.prototype
在 Object.prototype 里找到 toString → 返回，结束
若还没有，继续查 arr.__proto__.__proto__.__proto__ → 即 Object.prototype.__proto__ → null，停止

```
**最关键的记忆锚点：**  
==查找路径永远写成 **`__proto__` 的链式调用**（`obj.__proto__.__proto__...`）。  
**绝对不要**在普通实例后面接 `.prototype`，那是给函数准备的专用通道。你把“普通对象”和“函数”在内存中的角色彻底分开，这辈子就不会再混淆

（函数.prototype.xxxx本质是在给函数的实例写方法，而不是给函数自身写，对吗，也就是说，Array内部如果没有push，他自己是不能用他自己的push的）

### 2. new 做了什么
`new` 关键字右边**必须是函数（可调用对象）**，左边是接收这个新对象的变量（比如 `const a = new Animal()`）
> 要写：创建空对象 → 挂原型 → 绑定 this 执行 → 返回对象（4 步）。


本质：`new` 是引擎内置操作，==核心就是「造对象 + 接原型链 + 把构造函数当初始化器跑一遍 + 返回」==。

**“new 最关键的一次赋值，左边对象的 `__proto__` = 右边函数当前的 `prototype`（地址） ”**

```js
function Foo(name) { this.name = name; }
const f = new Foo('张三');
console(f.name)
```

```
① 申请堆 0x300，创建空对象 {}
② f.__proto__ = Foo.prototype（0x200）          ← 接上原型链
③ Foo.call(f, '张三')：this 绑定到 f，执行 this.name = '张三'
   → f = { name: '张三' }
④ Foo 没 return 对象 → 返回 f
```

手写 new：

```js
function myNew(Fn, ...args) {
  const obj = Object.create(Fn.prototype);  // ① ②：建空对象 + 挂原型，一步干完，在这之后,obj.__proto__ = Fn.prototype
  const res = Fn.apply(obj, args);          // ③：以 obj 为 this 执行构造函数
  return res instanceof Object ? res : obj; // ④：return 了对象就用它，否则返回 obj
}
```

Object.create 方法：**在堆上新建一个对象，然后把该对象的 `[[Prototype]]` 内部指针，强制指向你传入的 `proto` 对象。**
关键坑：==构造函数里若 `return` 一个对象，`new` 会返回那个对象、丢弃新建的 `f`==；`return` 基本类型则忽略，照常返回 `f`。


### 3. instanceof 原理
> 要写：沿原型链查找 `__proto__` 是否等于右边构造函数的 `prototype`。

instanceof(a,b)测试它左边的对象是否是它右边的的实例
本质：==`a instanceof B` 就是沿 `a.__proto__` 链表一路往上，逐个比对地址，看有没有等于 `B.prototype` 的==。找到 true，走到 `null` 还没找到 false。

```js
function myInstanceof(obj, Fn) {
  const prototype = Fn.prototype;
  let proto = Object.getPrototypeOf(obj);   // 拿 obj 的 [[Prototype]]
  while (proto) {
    if (proto === prototype) return true;   // 地址对上了
    proto = Object.getPrototypeOf(proto);   // 再上一层
  }
  return false;                             // 走到 null
}

myInstanceof([], Array);   // true
myInstanceof([], Object);  // true（Array.prototype 再往上就是 Object.prototype）
```

生产必考坑：==跨 iframe 失效==。每个 iframe / 窗口有独立的 `Array.prototype` 对象：

```js
const iframeArr = iframe.contentWindow.Array(1, 2, 3);
iframeArr instanceof Array;   // false ❌（本页面的 Array.prototype 不是它的原型）
```

所以大厂判类型不靠 instanceof，靠 `Object.prototype.toString`（lodash 的 `_.isArray` 底层就是它）——接上一节「类型判断」。



### 4. ES5 继承 vs class
> 要写：ES5 靠改原型链（`Object.create` + 修 constructor）；ES6 `class extends` 语法糖，`super` 作用。

先点破本质：==继承就两件事——① 拿父类的实例属性 ② 拿父类的原型方法==。

ES5 需要手动干两件，ES6 `class extends` 是语法糖，底层还是改原型链。

ES5 寄生组合式继承（最优，面试手写）：

```js
function Parent(name) {
  this.name = name;                    // 父类「实例属性」
}
Parent.prototype.say = function () {   // 父类「原型方法」
  console.log(this.name);
};

function Child(name, age) {
  Parent.call(this, name);   // ① 借父类构造函数，拿实例属性（this 是子类实例）
  this.age = age;
}
Child.prototype.__proto__ = Parent.prototype  // 1. 实例原型链连接（让 Child 的实例能访问 Parent.prototype 上的方法）

Child.prototype.constructor = Child;                // ③ 修正 constructor 指回
```

为什么用 `Object.create(Parent.prototype)` 而不是 `new Parent()`：后者会真的执行 Parent 构造函数、有副作用且拿不到 name 传参；前者只「抄一份原型链」、不执行构造函数。

地址图：
```
child 实例（堆 0x300）
  └─ __proto__ ─→ Child.prototype（0x400，Object.create 新建）
                    └─ __proto__ ─→ Parent.prototype（0x200）
                                      └─ __proto__ ─→ Object.prototype ─→ null
```

ES6 等价写法：
```js
class Parent {
    constructor(name) { this.name = name; }
}
class Child extends Parent {
    constructor(name, age) {
        super(name); // ★ 必须写！不写会报错：Must call super before accessing this
        this.age = age;
    }
}
```

`super` 两件事：
- `super(...)`：调父类构造函数，等价 `Parent.call(this, ...)`==
- `super.method()`：沿父类原型找方法，等价 `Parent.prototype.method.call(this)`==

两条硬规则：
1. 子类构造函数必须先 `super()` 才能用 `this`（this 由父类构造函数创建初始化，不调 super 就没有 this）。
2. `extends` 底层还多一步静态继承：`Child.__proto__ = Parent`（类自身的静态属性/方法也继承），ES5 手写最容易漏。


### ==`extends` 在底层干了什么？（两个方向的指针赋值）

当你写下 `class Child extends Parent` 时，引擎在**类定义阶段**（还没 `new`）自动执行了 ES5 时代老程序员手写的两行最麻烦的代码：

**底层伪代码（引擎自动执行）：**

```
javascript

// 1. 实例原型链连接（让 Child 的实例能访问 Parent.prototype 上的方法）
//    等价于 ES5 的：Child.prototype = Object.create(Parent.prototype)
Child.prototype.__proto__ = Parent.prototype; 
// 2. 静态方法继承（让 Child 能直接调用 Parent 的静态方法，如 Child.xxx）
//    等价于：Child.__proto__ = Parent
Child.__proto__ = Parent;

```

### ==`constructor` 在底层干了什么？（“组装车间”）

`constructor` 不是函数定义，它是**类实例化时的“初始化回调函数”**。当你 `new Child()` 时，引擎执行这个函数。

它的底层职责只有两件事：

1. **接收 `new` 传来的参数**。
2. **把参数挂到 `this` 身上**（以及执行你写的任何初始化逻辑）。

**底层等价于 ES5 的构造函数体：**

```

function Child(name, age) {
    // 这就是 constructor 的底层实体
    this.name = name;  // 你手动写的赋值
    this.age = age;
}
```



生产场景：自定义 Error 继承（axios / 业务错误封装）：

```js
class ApiError extends Error {
  constructor(message, code) {
    super(message);   // 必须先 super
    this.code = code;
  }
}
// 之后用 error instanceof ApiError 精确判错误类型，决定前端怎么提示
```


### ==Class干了什么？==
```

class TrackableArray { push(...items) {} }
等价于
// 1. 创建 TrackableArray 函数
function TrackableArray(...items) { ... }
// 2. ★ 把所有非静态方法挂到 TrackableArray.prototype 上 ★
TrackableArray.prototype.push = function(...items) { ... };
TrackableArray.prototype.constructor = TrackableArray;

```



## 五、事件循环 Event Loop ⭐

> 来源：https://juejin.cn/post/7520429827620519988 （作者 BUG收容所所长）

### 引言

你是否曾对 JavaScript 中 setTimeout 的"不准时"感到困惑？或者对 Promise 和 async/await 的执行顺序感到好奇？别担心，这些都是每个 JS 学习者都会遇到的"拦路虎"。今天，我将带你一起揭开它们背后的神秘面纱——事件循环（Event Loop）。

这篇博客将像一位耐心的向导，带你从最基础的"进程"和"线程"概念出发，一步步走进事件循环的核心，让你彻底搞懂 JS 的异步运行机制。准备好了吗？让我们开始这场奇妙的探索之旅吧！

### Part 1：故事的起点 —— 进程与线程

在我们深入 JS 世界之前，先来聊聊两个计算机世界的基本角色：

- **进程（Process）**：你可以把它想象成一个正在运行的"应用程序实例"。比如，你电脑上打开的微信、浏览器，每一个都是一个独立的进程。它有自己专属的内存空间，像一座独立的房子。
- **线程（Thread）**：如果进程是房子，那线程就是房子里的"工人"。一个进程可以有一个或多个工人（线程）同时干活。比如，在浏览器这个大房子里，有负责请求网络资源的工人（HTTP 请求线程）、有负责执行 JS 代码的工人（JS 引擎线程），还有负责把页面画出来的工人（渲染线程）。

重点来了：在浏览器中，JS 引擎线程 和 渲染线程 这两个工人是"死对头"，它们不能同时工作。为什么呢？想象一下，如果 JS 工人正在修改页面元素（操作 DOM），而渲染工人同时在绘制页面，那页面不就乱套了吗？所以，当 JS 工人在干活时，渲染工人就必须停下来等待，反之亦然。这就是所谓的"JS 引擎和渲染线程互斥"。

### Part 2：为什么需要异步？—— JS 的单线程宿命

我们知道 JS 的主要工作是操作 DOM、响应用户交互，这决定了它必须是单线程的。也就是说，JS 在同一时间只能做一件事。

这会带来一个问题：如果一个任务非常耗时（比如一个需要 5 秒的网络请求），那整个页面岂不是要卡住 5 秒钟，什么都干不了？这用户体验也太糟糕了！

为了解决这个问题，异步（Asynchronous）概念应运而生。JS 引擎（比如 Chrome 的 V8）想出了一个聪明的办法：

遇到耗时的异步任务，先不执行，而是把它挂起来，放到一个叫做"任务队列（Task Queue）"的地方。然后继续执行后面的同步代码。等到所有同步代码都执行完了，再回过头去看看任务队列里有没有需要处理的任务。

让我们来看个最简单的例子：

```js
let a = 1

setTimeout(() => {
  a = 2
  console.log(a) // 1秒后才会执行
}, 1000)

console.log(a) // 立刻执行
```

执行分析：

1. 代码从上到下执行，let a = 1。
2. 遇到 setTimeout，JS 引擎说："哦，这是个异步任务，我先不管你，把你丢到任务队列里等着。"
3. 继续向下执行，console.log(a)，此时 a 还是 1，所以控制台打印出 1。
4. 所有同步代码执行完毕。
5. 大约 1 秒后，setTimeout 的回调函数被从任务队列中取出并执行，a 被修改为 2，控制台打印出 2。

### Part 3：事件循环的核心 —— 宏任务与微任务

现在，我们来深入事件循环的核心。任务队列里的任务其实还分为两种：

- **宏任务（MacroTask）**：可以理解为比较"大"的任务。包括：setTimeout、setInterval、setImmediate (Node.js)、I/O 操作、UI rendering 等。
- **微任务（MicroTask）**：可以理解为比较"小"且需要尽快执行的任务。包括：Promise.then()、catch()、finally()、MutationObserver、process.nextTick (Node.js) 等。

事件循环的执行顺序非常严格，请一定记住这个规则：

1. 执行一个宏任务（通常是 script 脚本本身）。
2. 执行过程中，遇到宏任务就把它放到宏任务队列，遇到微任务就把它放到微任务队列。
3. 当前宏任务执行完毕后，立即检查微任务队列，并执行里面所有的微任务。
4. 所有微任务执行完毕后，如有需要，进行页面渲染。
5. 然后，从宏任务队列中取出一个任务，开始新一轮的循环。

让我们用一个经典的面试题来巩固一下：

```js
console.log(1); // 同步

new Promise((resolve) => {
  console.log(2); // 同步 (Promise构造函数是同步的)
  resolve();
})
  .then(() => { // 微任务
    console.log(3);
    setTimeout(() => { // 宏任务
      console.log(4);
    }, 0);
  });

setTimeout(() => { // 宏任务
  console.log(5);
  setTimeout(() => { // 宏任务
    console.log(6);
  }, 0);
}, 0);

console.log(7); // 同步
```

你能推断出正确的输出顺序吗？

答案是：1, 2, 7, 3, 5, 4, 6

执行分析：

1. 第一轮宏任务 (script) 开始执行。
2. console.log(1)，输出 1。
3. new Promise，构造函数立即执行，console.log(2)，输出 2。.then 的回调被放入微任务队列。
4. 遇到第一个 setTimeout，其回调被放入宏任务队列。
5. console.log(7)，输出 7。
6. ==同步代码执行完毕。检查微任务队列==，发现有一个 .then 的回调。
7. 执行微任务：console.log(3)，输出 3。在其中又遇到一个 setTimeout，其回调被放入宏任务队列。
8. 微任务队列清空。
9. 第一轮事件循环结束。
10. 第二轮宏任务开始，从宏任务队列中取出第一个任务（打印 5 的那个）。
11. console.log(5)，输出 5。又遇到一个 setTimeout，其回调被放入宏任务队列。
12. 第三轮宏任务开始，取出打印 4 的任务，console.log(4)，输出 4。
13. 第四轮宏任务开始，取出打印 6 的任务，console.log(6)，输出 6。

### Part 4：现代异步方案 —— async/await

async/await 是 Promise 的语法糖，让异步代码写起来更像同步代码。但它的本质没有变，仍然是基于事件循环。

- async 函数会返回一个 Promise。
- await 后面通常跟着一个返回 Promise 的表达式。它会"暂停" async 函数的执行，等待 Promise 状态变为 resolved，然后把 resolve 的值返回，并继续执行函数后面的代码。

重点来了：await 会将它后面的代码推入微任务队列。

来看这个例子：

```js
console.log("script start");

async function async1() {
    await async2(); // await 会阻塞后面的代码
    console.log("async1 end"); // 这行代码进入微任务队列
}

async function async2() {
    console.log("async2 end"); // 这行是同步代码
}

async1();

setTimeout(() => { // 宏任务
    console.log("setTimeout");
}, 0)

new Promise((resolve, reject) => { //你传的这个函数会在 new Promise 创建的一瞬间立即同步执行
    console.log("promise"); // 同步
    resolve();
})
    .then(() => { // 微任务
        console.log("then1");
    })
    .then(() => { // 微任务
        console.log("then2");
    });

console.log("script end");
```

正确输出顺序：script start, async2 end, promise, script end, async1 end, then1, then2, setTimeout

执行分析：

- 同步代码：script start -> async1() 调用 -> async2() 调用 -> async2 end -> promise -> script end。
- 微任务：await 后面的 async1 end，以及两个 .then 回调。按顺序执行：async1 end -> then1 -> then2。
- 宏任务：最后执行 setTimeout。

### Part 5：实用示例与常见问题解答

为了更好地理解事件循环，我们来看几个更复杂的例子，并解答一些初学者常遇到的问题。

**示例一：宏任务与微任务的交替执行**

```js
console.log("script start");

setTimeout(function() {
  console.log("setTimeout");
}, 0);

Promise.resolve().then(function() {
  console.log("promise1");
}).then(function() {
  console.log("promise2");
});

console.log("script end");
```

思考一下，这段代码的输出顺序是什么？

解析：

- console.log("script start")：同步代码，立即执行，输出 script start。
- setTimeout：宏任务，进入宏任务队列。
- Promise.resolve().then().then()：第一个 .then() 是微任务，进入微任务队列。当第一个 .then() 执行完毕后，其返回的 Promise 会立即将第二个 .then() 作为微任务放入微任务队列。
- console.log("script end")：同步代码，立即执行，输出 script end。

当前宏任务（同步代码）执行完毕。

- 检查微任务队列，执行第一个微任务 promise1，输出 promise1。此时，第二个 .then() 立即作为微任务进入微任务队列。
- 微任务队列中还有任务，继续执行第二个微任务 promise2，输出 promise2。

微任务队列清空。

- 检查宏任务队列，执行 setTimeout，输出 setTimeout。

最终输出：

```
script start
script end
promise1
promise2
setTimeout
```

这个例子再次强调了微任务在当前宏任务执行完毕后，会优先于下一个宏任务执行，并且微任务队列会在每个宏任务执行完毕后被完全清空。

**示例二：async/await 与 setTimeout 的结合**

```js
async function foo() {
  console.log("foo start");
  await bar();
  console.log("foo end");
}

async function bar() {
  console.log("bar");
}

console.log("script start");

setTimeout(function() {
  console.log("setTimeout");
}, 0);

foo();

new Promise(function(resolve) {
  console.log("promise constructor");
  resolve();
}).then(function() {
  console.log("promise then");
});

console.log("script end");
```

思考一下，这段代码的输出顺序是什么？

解析：

1. console.log("script start")：同步代码，输出 script start。
2. setTimeout：宏任务，进入宏任务队列。
3. foo() 调用：
   - console.log("foo start")：同步代码，输出 foo start。
   - await bar()：bar() 函数执行，输出 bar。await 暂停 foo 函数，并将 console.log("foo end") 作为微任务放入微任务队列。
4. new Promise(...)：Promise 构造函数中的代码是同步执行的，输出 promise constructor。resolve() 被调用，将 .then() 回调作为微任务放入微任务队列。
5. console.log("script end")：同步代码，输出 script end。
6. 当前宏任务（同步代码）执行完毕。

- 检查微任务队列，执行 foo 函数中 await 后面的微任务 console.log("foo end")，输出 foo end。
- 微任务队列中还有任务，执行 promise then，输出 promise then。

微任务队列清空。

- 检查宏任务队列，执行 setTimeout，输出 setTimeout。

最终输出：

```
script start
foo start
bar
promise constructor
script end
foo end
promise then
setTimeout
```

这个例子展示了 async/await 如何与 Promise 和 setTimeout 协同工作，以及微任务的优先级。

**常见问题解答**

- **Q1: 为什么 setTimeout(fn, 0) 不是立即执行？**
  A1: 尽管 setTimeout 的延迟时间设置为 0，但它仍然是一个宏任务。根据事件循环的规则，宏任务必须等待当前所有同步代码和微任务执行完毕后才能执行。因此，setTimeout(fn, 0) 只是表示将 fn 放入宏任务队列，等待下一个事件循环周期执行。

- **Q2: Promise.resolve().then() 和 process.nextTick() 有什么区别？**
  A2: 两者都是微任务，但 process.nextTick() 在 Node.js 环境中具有更高的优先级，它会在当前宏任务执行完毕后，微任务队列中的其他任务之前立即执行。在浏览器环境中，process.nextTick() 不可用，Promise.then() 是最常用的微任务。

- **Q3: 事件循环和浏览器渲染有什么关系？**
  A3: 浏览器渲染通常发生在微任务队列清空之后，下一个宏任务开始之前。这意味着，如果你在微任务中修改了 DOM，这些修改会在本次事件循环的渲染阶段被统一绘制到屏幕上。这有助于避免不必要的重复渲染，提高页面性能。

- **Q4: 如何避免 JavaScript 的阻塞？**
  A4: 避免 JavaScript 阻塞的关键在于合理利用异步编程。将耗时操作（如网络请求、大量计算）放入异步任务中，例如使用 Promise、async/await 或 setTimeout。这样可以确保主线程始终保持响应，提升用户体验。

### 总结

恭喜你！坚持看到了这里。现在，让我们来回顾一下今天学到的核心知识：

- JS 是单线程的，但通过事件循环机制实现了异步。
- 任务分为宏任务和微任务。
- 执行顺序是：一个宏任务 -> 所有微任务 -> (渲染) -> 下一个宏任务。
- ==Promise.then 和 await 后面的代码属于微任务，会优先于 setTimeout 等宏任务执行。==

理解事件循环是每一位前端开发者的必备内功。希望这篇博客能为你打下坚实的基础。如果你觉得有帮助，不妨分享给更多正在学习的小伙伴吧！


## 六、垃圾回收机制


垃圾回收算法(GC) 发生在 **内存分配导致空间不足时** 立即触发（Minor GC），或由引擎在 **事件循环的宏任务与宏任务之间的空闲时间** 主动调度执行增量标记（Major GC），系统内存压力过大时也会强制介入。

#### 1. 核心算法：标记-清除 (Mark-Sweep)

- **从根 (Roots) 出发**：GC 从全局对象、栈上的变量等根引用出发，遍历所有对象。
- **标记活物**：能被遍历**到达 (Reachable)** 的对象标记为“存活”。
- **清除垃圾**：**未被标记 (Unreachable)** 的对象（即没有任何引用指向的内存块）将被内存回收。
  

#### 2. V8 优化策略：分代回收 (Generational GC)

基于“绝大部分对象朝生夕死”的统计学规律，V8 将堆内存分为两代：

- **新生代 (Young Generation)**：存放新创建的对象。采用 **Scavenge (复制算法)**，空间换时间，回收极快。存活下来的对象晋升至老生代。
  
- **老生代 (Old Generation)**：存放存活时间长的对象（如全局变量、闭包变量）。采用 **标记-整理 (Mark-Compact)** 算法，将存活对象移到一端，清理边界外的内存，防止内存碎片。
  

#### 3. 常见内存泄漏场景 (避免踩坑)

- **意外的全局变量**：`function() { a = 1; }`（`a` 挂在 `window` 上无法回收）。
- **未清除的定时器/事件监听**：`setInterval` 或 `addEventListener` 移除前，其引用的 DOM 或变量不会被回收。
- **闭包滥用**：闭包长期持有大数组或 DOM 节点的引用，导致其无法释放。


## 七、常用语法细节

### 1. for...in vs for...of
> 要写：`for...in` 遍历可枚举键（含原型链、字符串键，不适合数组）；`for...of` 遍历可迭代对象的值（适合数组/Map/Set，拿不到索引）。


### 2. 可选链 ?. 与空值合并 ??
> 要写：`?.` 短路避免 `undefined.xxx` 报错；`??` 只在 null/undefined 时取默认值（与 `||` 区别）。


### 3. var / let / const 与 TDZ
> 要写：`var` 函数作用域 + 变量提升；`let/const` 块级作用域 + 暂时性死区（TDZ）；`const` 不能重新赋值但对象属性可改。


### 4. 解构赋值
> 要写：数组/对象解构、默认值、剩余参数 `...`、函数参数解构。


## 八、ES2023+ 新特性（常考，别显老）

### 1. 非破坏性数组方法（ES2023）
> 要写：`toSorted / toReversed / toSpliced / with` —— 返回新数组，不改原数组；与 `sort/reverse/splice` 区别。


### 2. 分组 Object.groupBy / Map.groupBy（ES2024）
> 要写：按回调结果给数组分组，返回对象或 Map；替代手写 reduce 分组。


### 3. Promise.withResolvers / Array.fromAsync（ES2024）
> 要写：`Promise.withResolvers()` 一次性拿到 promise + resolve + reject；`Array.fromAsync` 从异步可迭代对象建数组。


### 4. Temporal（新日期 API，最高频）
> 要写：为什么替代 Date（月份不再从 0 开始、不可变、原生时区、`.add/.subtract/.since` 日期计算）；`Temporal.PlainDate.from('2026-03-25')`。


### 5. Import Attributes（ES2025）
> 要写：`import data from './x.json' with { type: 'json' }` 声明导入类型；解决扩展名不可靠的安全问题。


### 6. 逻辑赋值运算符
> 要写：`??=`、`||=`、`&&=` 的含义和示例。


### 7. Decorators（Stage 3）
> 要写：类字段/方法/访问器装饰器；与旧版 @decorator 写法差异；AOP 思想。


### 8. 其他工程常用
> 要写：`structuredClone` 深拷贝、`Error.isError`（跨 iframe 判断）、`Math.sumPrecise` 浮点求和、`Uint8Array` 的 `toBase64/toHex`。


## 基础语法速查（面试优先级低）

变量声明、运算符、控制结构、数组方法（`map/filter/reduce/find/includes`）、DOM 操作——不深挖，能写即可。


**链表委托**


### 2. 为什么说“不对”？（ES6+ 现代语法视角）

虽然底层是原型，但 ES6 之后，官方推出了 `class` 关键字。**从语法层面（开发者使用层面）来讲，JS 现在明确地区分类和对象。**

- **类（Class）**：是抽象的设计图（`class Dog {}`）。
  
- **对象（Object / Instance）**：是根据类具体造出来的实例（`new Dog()`）。
  

虽然底层 `class` 会被转译为函数，但语言官方给了它“一等公民”的地位，并且在语法上强制区分：

- 类必须 `new` 调用，不能像普通函数那样随意调用（`Dog()` 会报错）。
  
- 类有专门的 `constructor`、`static`、`#私有字段` 等语法。
  

所以，如果你在面试时答“JS不区分类和对象”，面试官大概率会说“你落伍了”，因为现在 **`class` 和 `new` 已经是 JS 的标准面相对象写法**。



ES6新特性