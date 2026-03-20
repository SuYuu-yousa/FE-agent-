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

TypeScript

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

TypeScript

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

## 三、结合 React 基础举一个完整 TSX 例子

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

React

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