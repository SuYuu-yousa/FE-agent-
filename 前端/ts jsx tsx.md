# TypeScript（面试版）

> 目标：JD 要求"深入理解 TS"。高频集中在 `interface vs type`、泛型、工具类型、条件类型 + `infer`、`any/unknown/never`，另补 TS 5.x 实用新特性。每个 `###` 用自己的话填。

## 核心区别对比

### TS vs JS
> 要写：静态类型 vs 动态类型、编译时检查、类型擦除（编译后类型信息完全消失，运行时就是 JS）。


## 一、基础类型与注解

### 1. 原始类型
> 要写：`boolean / number / string / symbol / bigint`；`let name: string`。


### 2. 数组 / 元组 / 对象
> 要写：`number[]` / `Array<number>`；元组 `[string, number]`；对象用 interface 或 type 描述形状。


### 3. void / undefined / null
> 要写：`void`（函数无返回值）、`undefined` / `null`、`strictNullChecks` 下二者需显式声明。


## 二、interface vs type ⭐

### 1. 两者区别
> 要写：`interface` 描述对象形状、可声明合并（同名自动合并）、可 `extends`/`implements`；`type` 可做联合/交叉/别名原始类型/元组。


### 2. 声明合并
> 要写：两个同名 `interface` 自动合并属性；`type` 不能重复声明。


### 3. 何时用哪个
> 要写：描述对象/类的形状用 interface，需要联合类型/工具类型/函数类型用 type；团队约定优先。


## 三、泛型 ⭐

### 1. 泛型函数
> 要写：`identity<T>(arg: T): T`；解决"既要类型安全又要复用"。


### 2. 泛型约束
> 要写：`<T extends { length: number }>` 限制 T 必须有某属性。


### 3. 泛型接口 / 类型
> 要写：`interface ApiResponse<T> { data: T }`；泛型默认值 `T = unknown`。


## 四、工具类型 ⭐

### 1. Partial / Required / Readonly
> 要写：分别把属性变可选 / 必选 / 只读。


### 2. Pick / Omit
> 要写：`Pick<User, 'id'|'name'>` 选取；`Omit<User, 'email'>` 排除。


### 3. Record
> 要写：`Record<string, string[]>` 构建键值对类型。


### 4. Exclude / Extract / NonNullable
> 要写：`Exclude<T, U>` 从 T 排除 U；`Extract<T, U>` 取交集；`NonNullable` 去掉 null/undefined。


### 5. ReturnType / Parameters / Awaited
> 要写：`ReturnType<typeof fn>` 取返回类型；`Parameters<typeof fn>` 取参数元组；`Awaited<T>` 提取 `Promise<T>` 结果（比手写 PromiseResult 更常用）。


## 五、条件类型与 infer ⭐

### 1. 条件类型
> 要写：`T extends U ? X : Y`；分布式条件类型（裸类型参数会逐个分发）。


### 2. infer
> 要写：在条件类型里声明类型变量并提取，`T extends (infer R)[] ? R : never`。


### 3. 手写工具类型
> 要写：手写 `MyReturnType<T>`、`MyParameters<T>`、`PromiseResult<T>`（提取 `Promise<T>` 的 `T`）。


## 六、any / unknown / never ⭐

### 1. any
> 要写：放弃类型检查的"逃生舱"，会传染、不安全，尽量少用。


### 2. unknown
> 要写：类型安全的 any，用前必须收窄（typeof/断言），适合 unknown 输入。


### 3. never
> 要写：永远不存在的值（抛错函数、死循环、穷尽检查 default 分支）；`never` 是任何类型的子类型。


## 七、类型守卫与收窄

### 1. typeof / instanceof / in
> 要写：用这些把联合类型收窄到具体类型。


### 2. 自定义类型谓词 is
> 要写：`function isString(x: unknown): x is string`；`is` 关键字的作用。


## 八、TS 5.x 实用新特性（常考）

### 1. satisfies 运算符（TS 4.9）
> 要写：`const x = {...} satisfies T` 校验类型但保留字面量推断；与类型注解的区别。


### 2. const 类型参数（TS 5.0）
> 要写：`function f<const T>(x: T)` 相当于给 T 加 `as const`，保留字面量类型。


### 3. keyof / typeof
> 要写：`keyof T` 取键的联合类型；`typeof` 在 TS 里取值的类型（`typeof obj`）。


### 4. 模板字面量类型
> 要写：`` type E = `${'a'|'b'}-${number}` `` 构造字符串字面量类型。


### 5. as const
> 要写：`as const` 让对象/数组变成只读、字面量类型；常用于配置常量。


## 九、类型安全与 schema（关联 JD）

### 1. Zod 与 tool calling
> 要写：为什么 Agent 的 tool calling 要用 Zod 等做 schema 校验（保证前端传参和模型要求的 schema 严格匹配）。


### 2. 流式响应嵌套类型
> 要写：用泛型 + 条件类型 + `infer` 处理 AI 流式响应的嵌套结构（如 `choices[0].delta.content`）。


## TSX / JSX 与 React（周四深挖）

### JSX 是什么
> 要写：JS 语法扩展，编译成 `React.createElement(...)`。


### .tsx vs .ts
> 要写：`.tsx` 是含 JSX 的 TS（组件），`.ts` 是纯逻辑/类型文件。


### React + TS 计数器
> 要写：`React.FC<CounterProps>` 泛型组件类型；`useState<number>` 泛型 Hook；传错 props 编译直接报错。完整逐行拆解旧稿在 git 历史，周四 React 篇展开。
