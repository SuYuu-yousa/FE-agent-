
我们用你给的 `TrackableArray` 例子，加上静态方法和静态属性，把 `class` 语法糖**完整地、逐行地**翻译成 ES5 时代的“手写函数”版本。

**先上带静态成员的 `class` 代码：**
```javascript
class TrackableArray extends Array {
    // ① 静态属性（类变量）
    static type = 'trackable';
    
    // ② 静态方法（类方法）
    static createFrom(arr) {
        return new TrackableArray(...arr);
    }
    
    // ③ 实例构造函数
    constructor(...items) {
        super(...items);      // 调用父类构造函数
        this._trackId = Date.now();
    }
    
    // ④ 实例方法（覆盖父类）
    push(...items) {
        const result = super.push(...items);
        console.log(`[Track ${this._trackId}] Pushed ${items.length}`);
        return result;
    }
}
```

---

### 逐行翻译成“去糖”后的 ES5 代码（底层本质）

```javascript
// ==========================================
// 第 1 步：定义构造函数（对应 constructor）
// ==========================================
function TrackableArray(...items) {
    // ★ 糖 1：super(...items) 被翻译为显式调用父构造函数，并绑定 this
    //    注意：Array 是内置函数，这里用 Array.call
    Array.call(this, ...items); 
    
    // 实例属性
    this._trackId = Date.now();
}

// ==========================================
// 第 2 步：处理 extends（连接两条原型链）
// ==========================================
// ① 实例链：让 TrackableArray 的实例，能访问 Array.prototype 上的方法
//    等价于：TrackableArray.prototype.__proto__ = Array.prototype
TrackableArray.prototype = Object.create(Array.prototype);
// 手动修正 constructor 指针（class 语法自动帮你修）
TrackableArray.prototype.constructor = TrackableArray;

// ② 静态链：让 TrackableArray 能访问 Array 的静态方法（如 Array.from）
//    ★ 这是很多人忽略的糖！class 的 extends 自动干了这行
TrackableArray.__proto__ = Array;

// ==========================================
// 第 3 步：挂载实例方法（对应 push）
// ==========================================
TrackableArray.prototype.push = function(...items) {
    // ★ 糖 2：super.push(...items) 被翻译为硬编码调用父原型上的方法
    //    注意：这里用的是 Array.prototype.push，不走原型链查找！
    const result = Array.prototype.push.call(this, ...items);
    console.log(`[Track ${this._trackId}] Pushed ${items.length}`);
    return result;
};

// ==========================================
// 第 4 步：挂载静态方法（对应 static createFrom）
// ==========================================
// ★ 糖 3：static 方法直接被挂载到构造函数自身（函数对象上）
TrackableArray.createFrom = function(arr) {
    return new TrackableArray(...arr);
};

// ==========================================
// 第 5 步：挂载静态属性（对应 static type）
// ==========================================
// ★ 糖 4：static 属性同样直接被挂载到构造函数自身
TrackableArray.type = 'trackable';
```

---

### 对照表：`class` 语法到底“糖”了哪些手动操作？

| `class` 里的写法 | 底层“去糖”后的本质 | 你以前要手写的麻烦事 |
| :--- | :--- | :--- |
| `constructor(...items) { ... }` | 定义名为 `TrackableArray` 的函数体 | 手动创建构造函数，并记得把 `prototype.constructor` 指回来 |
| `super(...items)` | `ParentConstructor.call(this, ...items)` | 手动在子构造函数里写 `Array.call(this, ...)`，还得处理 `arguments` 的传递 |
| `static createFrom() {}` | `TrackableArray.createFrom = function() {}` | 手动把函数当成普通属性挂到构造函数上 |
| `static type = 'trackable'` | `TrackableArray.type = 'trackable'` | 手动给构造函数赋值（ES2022 之前甚至没有这个语法，得写在类外面） |
| `push(...items) {}`（实例方法） | `TrackableArray.prototype.push = function(...items) {}` | 手动往 `prototype` 上挂载方法 |
| `super.push(...items)` | `Array.prototype.push.call(this, ...items)` | 手动用 `call` 调用父原型方法，并传入当前 `this` |
| `extends Array` | **同时做了两件事**：<br>① `TrackableArray.prototype = Object.create(Array.prototype)`<br>② `TrackableArray.__proto__ = Array` | ① 手动用 `Object.create` 或 `F` 桥梁接原型链<br>② 手动让子类构造函数继承父类构造函数（实现静态方法继承） |
| `new TrackableArray(...)` | 1. 创建空对象 `{}`<br>2. 把空对象的 `__proto__` 指向 `TrackableArray.prototype`<br>3. 执行构造函数体<br>4. 返回对象 | 手动实现 `Object.create` + 手动 `apply` 调用构造函数（就是你自己写的那个 `myNew`） |

---

### 重点拆解你代码里两个“最甜的糖”

**1. `super.push(...items)` 为什么翻译成 `Array.prototype.push.call(this, ...items)`？**
因为你在 `TrackableArray.prototype` 上覆盖了 `push`。如果在 `push` 方法内部写 `this.push(...items)`，就会**无限递归**（永远调用自己）。`super.push` 在编译时就被引擎硬编码为“**忽略当前类原型，直接去父原型上找**”，从而安全地调用原生 `push`。

**2. `extends Array` 为什么把 `TrackableArray.__proto__` 也指向 `Array`？**
因为 `Array` 有静态方法，比如 `Array.from()`。如果不把 `TrackableArray.__proto__` 指向 `Array`，那么 `TrackableArray.from` 就是 `undefined`，你就没法像 `Array.from([1,2])` 那样用 `TrackableArray.from([1,2])` 来构造实例。

---

### 终极总结（记住这两句就不用背语法）

- **`class` 语法糖的本质：** 把“构造函数体”、“原型方法”、“静态成员”、“父子连线”这四堆代码，**收拢到一个大括号里**，并自动帮你补上 `prototype.constructor`、`Object.create` 和 `__proto__` 双链连接。
- **`super` 的本质：** 在编译阶段就被替换为**父构造函数**（`constructor` 场景）或**父原型对象**（方法场景）的显式 `.call` 调用，不走动态原型链查找，确保你在覆盖方法时还能正常调用底层原始逻辑。

现在，你脑海里的 `class TrackableArray extends Array` 已经不再是模糊的关键字，而是一组**确定的内存指针赋值和函数调用语法糖**了。
</details>


### 问题一：为什么 `push` 在 `TrackableArray.prototype` 上？

**本质结论：这是 `class` 语法在底层被编译时的固定规则。**

当你写 `class TrackableArray { push(...items) {} }` 时，引擎在**类定义阶段**执行的伪代码等价于：

javascript

// 1. 创建 TrackableArray 函数
function TrackableArray(...items) { ... }
// 2. ★ 把所有非静态方法挂到 TrackableArray.prototype 上 ★
TrackableArray.prototype.push = function(...items) { ... };
TrackableArray.prototype.constructor = TrackableArray;

**内存真相：** `push` 方法**不是**挂在 `TrackableArray` 函数自己身上，而是挂在 `TrackableArray.prototype` 这个对象身上。这是 `class` 语法糖的固定编译规则。

---

### 问题二：`new TrackableArray(1, 2, 3)` 发生什么？（完整底层四步）

按时间顺序，用你熟悉的指针地址模拟：

**第 1 步（分配内存）：** 引擎在堆上**新建一个空白对象**，假设地址为 `0x2000`。

**第 2 步（设置原型指针）：** 把 `0x2000` 对象的 `[[Prototype]]` 内部槽，**赋值为当前 `TrackableArray.prototype` 的地址**。  
因为 `extends` 已经设置了 `TrackableArray.prototype.__proto__ = Array.prototype`，所以这个新对象的原型链是：`0x2000` → `TrackableArray.prototype` → `Array.prototype` → `Object.prototype`。

**第 3 步（执行构造函数）：** 引擎调用 `TrackableArray` 的 `constructor` 函数，并把 `this` 绑定为 `0x2000`。

- 先执行 `super(...items)`：这等于 `Array.prototype.constructor.apply(this, items)`。`Array` 构造函数会往 `this`（即 `0x2000`）的自身属性表里写入 `0: 1`、`1: 2`、`2: 3`、`length: 3`。
    
- 再执行 `this._trackId = Date.now()`：往 `0x2000` 的自身属性表里新增键 `_trackId`。
    

**第 4 步（返回）：** 如果构造函数没有返回其他对象，引擎把 `0x2000` 的地址返回，赋值给 `list`。

**此时 `list` 自身存有：** `0`、`1`、`2`、`length`、`_trackId`。**绝对没有 `push`。**

---

### 问题三：`list.push(4)` 为什么能调用？（原型链查找路径）

引擎执行属性访问 `list.push` 时，严格按照 `OrdinaryGet` 算法：

1. **查自身（`0x2000`）**：遍历 `list` 自身的哈希表，键有 `0`、`1`、`2`、`length`、`_trackId`。**没有 `push`** → 未命中。
    
2. **查 `list.__proto__`（即 `TrackableArray.prototype`）**：解引用内部原型指针，跳到 `TrackableArray.prototype` 这块内存。  
    这块内存的哈希表里有 `constructor`、`push`（你定义的那个）。**命中 `push`** → 取出这个函数。
    
3. **执行取出的函数**：把 `this` 绑定回 `list`（即 `0x2000`），传入参数 `4`。
    

**所以答案是：** `list.push(4)` 命中了你写在 `TrackableArray.prototype` 上的那个 `push` 方法，因为 `list` 自身没有 `push`，顺着原型链往上爬了一层就找到了。

---

### 隐藏但必须澄清的一点：`super.push` 与 `list.push` 的差异

你可能潜意识里有个疑惑：_“你自己定义了 `push`，把原生的覆盖了，那 `super.push` 为什么还能调用原生方法？”_

**底层真相（编译期静态绑定）：**

- `list.push` → 是在**运行时**沿着 `list` 的原型链动态查找（你找到的是 `TrackableArray.prototype` 上的方法）。
    
- **`super.push`** → 是在**编译 `class` 时的词法作用域**中静态确定的。引擎在编译 `TrackableArray` 类时，就把 `super` 关键字硬编码为**当前类的父原型**，即 `Array.prototype`。所以 `super.push(...)` 在底层直接等价于 `Array.prototype.push.apply(this, ...)`，**完全不经过原型链查找**。