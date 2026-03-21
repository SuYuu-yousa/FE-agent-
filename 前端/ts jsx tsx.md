# TS
## 核心区别对比

|特性|JavaScript|TypeScript|
|---|---|---|
|类型系统|动态类型|静态类型（编译时检查）|
|编译|直接运行|需要编译为 JS 再运行|
|文件扩展名|`.js`|`.ts` / `.tsx`|
|类型注解|❌|✅|
|接口（Interface）|❌|✅|
|泛型（Generics）|❌|✅|
|枚举（Enum）|❌|✅|
|IDE 智能提示|较弱|非常强大|
|学习曲线|低|稍高|
## TypeScript 核心特性详解

### 1. 类型注解（Type Annotation）

```
// JS：运行时才发现错误
let name = "Tom";
name = 123; // JS 不报错，运行时可能出 bug

// TS：编译时就能发现错误
let name: string = "Tom";
name = 123; // ❌ 编译报错：Type 'number' is not assignable to type 'string'
```

### 2. 基础类型

TypeScript

```
let isDone: boolean = false;
let count: number = 42;
let username: string = "Alice";
let list: number[] = [1, 2, 3];
let tuple: [string, number] = ["hello", 10]; // 元组
let anything: any = "可以是任何类型";  // 逃生舱，尽量少用
let nothing: void = undefined;  // 通常用于函数无返回值
let u: undefined = undefined;
let n: null = null;
```

### 3. 接口（Interface）

TypeScript

```
// 定义对象的"形状"
interface User {
  id: number;
  name: string;
  age?: number;        // 可选属性
  readonly email: string; // 只读属性
}

//函数后面加个冒号说明返回类型，可以不写，自动推断，可以void 
function greet(user: User): string {
  return `Hello, ${user.name}`;
}

// ✅ 正确
greet({ id: 1, name: "Tom", email: "tom@example.com" });

// ❌ 编译报错：缺少必要属性
greet({ name: "Tom" });
```

### 4. 泛型（Generics）


```
// 不用泛型 —— 要么丢失类型，要么写多个函数
function identity(arg: any): any {
  return arg; // 返回类型信息丢失了
}

// 用泛型 —— 灵活且类型安全
function identity<T>(arg: T): T {
  return arg;
}

let output1 = identity<string>("hello");  // 类型是 string
let output2 = identity<number>(42);       // 类型是 number
let output3 = identity("hello");          // 自动推断为 string
```
## 泛型用在接口/类型上

```
// 一个通用的 API 响应格式
interface ApiResponse<T> {
  code: number;
  message: string;
  data: T;          // data 的类型是灵活的
}

// 用户接口返回
const res1: ApiResponse<string> = {
  code: 200,
  message: "ok",
  data: "hello"       // data 是 string
};

// 用户列表接口返回
interface User { id: number; name: string; }

const res2: ApiResponse<User[]> = {
  code: 200,
  message: "ok",
  data: [              // data 是 User[]
    { id: 1, name: "Tom" },
    { id: 2, name: "Jerry" }
  ]
};
```

### 5. 枚举（Enum）
```
enum Direction {
  Up,      // 0
  Down,    // 1
  Left,    // 2
  Right    // 3
}

enum HttpStatus {
  OK = 200,
  NotFound = 404,
  ServerError = 500
}

let dir: Direction = Direction.Up;
```

### 6. 联合类型 & 交叉类型

```
// 联合类型：可以是多种类型之一
let id: string | number;
id = "abc";  // ✅
id = 123;    // ✅
id = true;   // ❌

// 交叉类型：合并多个类型
interface HasName { name: string; }
interface HasAge { age: number; }

type Person = HasName & HasAge;
// Person 必须同时有 name 和 age
```

### 7. 类型别名（Type Alias）


```
type ID = string | number;
type Point = { x: number; y: number };
type Callback = (data: string) => void;
```

### 8. 类型推断 & 类型守卫

```
// 类型推断：TS 自动推断类型，不必处处写注解
let x = 10;        // 推断为 number
let arr = [1, 2];  // 推断为 number[]

// 类型守卫（Type Guard）
function printId(id: string | number) {
  if (typeof id === "string") {
    console.log(id.toUpperCase()); // 这里 TS 知道 id 是 string
  } else {
    console.log(id.toFixed(2));    // 这里 TS 知道 id 是 number
  }
}
```

### 9. 高级类型工具（Utility Types）

```
interface User {
  id: number;
  name: string;
  email: string;
}

// Partial：所有属性变可选
type PartialUser = Partial<User>;

// Pick：只选取部分属性
type UserBasic = Pick<User, "id" | "name">;

// Omit：排除某些属性
type UserWithoutEmail = Omit<User, "email">;

// Record：构建键值对类型
type RoleMap = Record<string, string[]>;
```

## 编译流程


```
  .ts 文件
     │
     ▼
  TypeScript 编译器 (tsc)  ──→  类型检查（报错在此阶段）
     │
     ▼
  .js 文件  ──→  浏览器 / Node.js 运行
```

> **重点理解**：TS 的类型信息在编译后**完全擦除**，运行时和普通 JS 没有区别。

### TS React 项目（主流做法）

- 逻辑工具代码：`utils.ts`
- React 组件：`App.tsx`, `Button.tsx`

这样区分有几个好处：

- `.tsx`：一眼看出这是个**组件/含 JSX 的文件**
- `.ts`：一眼看出这是**纯逻辑/类型定义**文件


# JSX
JSX 是：

> ✅ 一种 JS 的语法扩展  
> ✅ 允许你在 JS 里写类似 HTML 的结构  
> ✅ 最终会被编译成 `React.createElement(...)`

# TSX



# 一个例子

## 结合 React 基础举一个完整 TSX 例子

假设我们做一个简单的 React + TS 计数器组件：`Counter.tsx`


```
// Counter.tsx
import React, { useState } from "react";

// 1. 定义 props 的类型
interface CounterProps {
  initialValue?: number; // 可选
}

// 2. 函数组件：使用 TSX + 类型标注
const Counter: React.FC<CounterProps> = ({ initialValue = 0 }) => {
  // useState<number> 表示 count 是 number 类型
  const [count, setCount] = useState<number>(initialValue);

  const handleClick = () => {
    setCount(count + 1);
  };

  // 3. return 的是 JSX
  return (
    <div>
      <p>当前计数：{count}</p>
      <button onClick={handleClick}>+1</button>
    </div>
  );
};

export default Counter;
```

### 对比一下如果是 JS + JSX（无 TS）

```
// Counter.jsx
import React, { useState } from "react";

const Counter = ({ initialValue = 0 }) => {
  const [count, setCount] = useState(initialValue); // 类型随便，可能被传错

  const handleClick = () => {
    setCount(count + 1);
  };

  return (
    <div>
      <p>当前计数：{count}</p>
      <button onClick={handleClick}>+1</button>
    </div>
  );
};

export default Counter;
```

差异：

- `.tsx` 版本里：
    - `CounterProps` 明确了 `initialValue` 是 `number | undefined`
    - 如果你在别处写 `<Counter initialValue="abc" />`，TS 会直接报错
- `.jsx` 版本里：
    - 你写 `<Counter initialValue="abc" />`，不会有编译错误，运行时可能表现异常


很好 👍 这段代码基本涵盖了 **React + TypeScript 函数组件**的核心写法。  
我给你逐行、逐概念拆解，并且把 JS / TS / React 各自的角色讲清楚。

---

# 整体结构先看一眼



```
const Counter: React.FC<CounterProps> = ({ initialValue = 0 }) => {
  const [count, setCount] = useState<number>(initialValue);

  const handleClick = () => {
    setCount(count + 1);
  };

  return (
    <div>
      <p>当前计数：{count}</p>
      <button onClick={handleClick}>+1</button>
    </div>
  );
};
```

---

# 第一行

```
const Counter: React.FC<CounterProps> = ...
```

拆成三部分：

---

## 1️⃣ `const Counter`

定义一个常量，名字叫 `Counter`。

在 React 里：

> ✅ 函数组件本质就是一个函数  
> ✅ 组件名必须大写（React 通过首字母判断是组件还是 HTML 标签）

---

## 2️⃣ `: React.FC<CounterProps>`

这是 TypeScript 类型标注。

意思是：

> Counter 是一个 React 函数组件（Function Component）  
> 它的 props 类型是 `CounterProps`

### React.FC 是什么？

`React.FC` 是一个泛型类型：

```
React.FC<P>
```

意思是：

> 一个接收 P 类型 props 的函数组件

这里：

```
React.FC<CounterProps>
```

表示：

> 这个组件接收的 props 必须符合 CounterProps 接口

例如你可能有：

```
interface CounterProps {
  initialValue?: number;
}
```

---

## 3️⃣ `= ({ initialValue = 0 }) => {`

这是一个箭头函数。

### 发生了什么？

组件本质是：

TypeScript

```
(props: CounterProps) => JSX
```

但这里做了**结构赋值 + 默认值**

```
({ initialValue = 0 })
```

等价于：

```
(props) => {
  const initialValue = props.initialValue ?? 0;
}
```

意思是：

- 从 props 里取出 initialValue
- 如果没传，默认是 0

✅ 这是 JS 语法  
✅ TS 只是做类型约束

---

# 第二部分

```
const [count, setCount] = useState<number>(initialValue);
```

这是 React Hook。

---

## 1️⃣ useState 是什么？

`useState` 是 React 提供的 Hook：

```
function useState<T>(initialState: T): [T, Dispatch<SetStateAction<T>>]
```

注意这里：

✅ 它本身是一个泛型函数

---

## 2️⃣ `<number>` 是什么？

```
useState<number>
```

这是泛型参数。

意思是：

> 这个 state 的类型是 number

---

## 3️⃣ `(initialValue)`

初始值是 initialValue。

因为 initialValue 是 number（由 props 类型保证），所以：

```
T = number
```

---

## 4️⃣ `[count, setCount]`

这是数组解构。

`useState` 返回：

```
[当前状态, 更新状态的函数]
```

所以：

|变量|含义|
|---|---|
|count|当前状态值|
|setCount|修改状态的函数|

---

## 状态更新的本质

```
setCount(count + 1);
```

不是直接改变量。

React 内部会：

1. 更新状态
2. 触发组件重新执行
3. 重新渲染 JSX

---

# 第三部分

```
const handleClick = () => {
  setCount(count + 1);
};
```

定义一个函数。

它做的事情：

> 点击按钮时，把 count + 1

这是普通 JS 函数，没有 TS 特殊语法。

TS 会自动推断：

```
handleClick: () => void
```

---

# 第四部分：return JSX

```
return (
  <div>
    <p>当前计数：{count}</p>
    <button onClick={handleClick}>+1</button>
  </div>
);
```

这里是 JSX。

---

## `<div>...</div>`

JSX 语法。

会被编译成：

JavaScript

```
React.createElement("div", ...)
```

---

## `{count}`

JSX 表达式插值。

意思是：

> 在 UI 中插入 JS 表达式的值

当 count 变化时，UI 自动更新。

---

## `onClick={handleClick}`

给 button 绑定点击事件。

JSX 里的事件是：

- 驼峰写法
- 传函数引用

等价于：

JavaScript

```
button.addEventListener("click", handleClick)
```

---

# 整个组件执行流程（非常重要）

1️⃣ 组件第一次执行  
→ useState 创建状态  
→ 返回 JSX  
→ React 渲染 UI

2️⃣ 点击按钮  
→ 执行 handleClick  
→ 调用 setCount  
→ React 更新状态

3️⃣ 组件重新执行  
→ 生成新的 JSX  
→ React 对比虚拟 DOM  
→ 更新页面

---

# TypeScript 在这里做了什么？

TS 只做了一件事：

> 在编译阶段检查类型

比如：

React

```
<Counter initialValue="hello" />
```

会报错：

text

```
Type 'string' is not assignable to type 'number'
```

---

# 整体拆解总结

|部分|是什么|属于谁|
|---|---|---|
|const Counter|定义函数组件|JS|
|React.FC|函数组件类型|TS|
|<CounterProps>|泛型参数|TS|
|({ initialValue = 0 })|解构 + 默认值|JS|
|useState<number>|泛型 Hook|React + TS|
|[count, setCount]|数组解构|JS|
|handleClick|事件函数|JS|
|JSX|UI 语法糖|React|
|{count}|表达式插值|JSX|