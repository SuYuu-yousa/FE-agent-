# JavaScript 基础知识简明指南

## 核心概念
从语言构成的角度，JavaScript的基本构造包括：

1. 原始值（上述7种类型）。
2. 对象（普通对象、数组、函数、正则、日期、错误、Map、Set 等内置对象，以及自定义对象）。
3. 操作符（如 +、-、typeof、instanceof、new 等）。
4. 语句（if、for、while、return、try 等）。
5. 表达式（字面量、标识符、函数调用等）。
6. 声明（变量声明 var/let/const，函数声明 function，类声明 class，模块导入 import 等）。


## 原型与原型链

js里的“数据”，除了基本类型，都是“对象”

对象是什么概念呢?就是object这个东西的派生，他们都通过原型proto链连接到object上

proto是什么?他的值等于：构造该对象的构造函数的prototype属性。所有对象都有proto，说明什么?所有对象都是或间接是某个“构造函数”创造的(除了object花活做出来的proto是null)

function也是一类object，也就是说函数本体也是一个对象，这个本体的原型也是对象(Function.proto=object.prototype
object.proto = null )

大多数自定义的结构是function foo实际上就做了把Function这个类型实例化一个foo

prototype是什么？是一个map，你可以往里面加你想给这个函数实例用的东西，prototype的proto，默认是object.prototype，但如果有继承关系，要改为是这个prototype所属构造函数的父函数的prototype
```javascript

// 1. 定义 Creature 构造函数（基类）
function Creature(name) {
  this.name = name; // 实例属性：名字
}
// 在 Creature.prototype 上添加共享方法
Creature.prototype.breathe = function() {
  console.log(`${this.name} is breathing.`);
};

// 2. 定义 Animal 构造函数（继承 Creature）
function Animal(name, legs) {
  // 借用构造函数继承属性
  Creature.call(this, name);
  this.legs = legs; // 实例属性：腿的数量
}
// 设置 Animal.prototype 继承 Creature.prototype
Animal.prototype = Object.create(Creature.prototype);
// 修复 constructor 指向，确保一致性
Animal.prototype.constructor = Animal;
// 在 Animal.prototype 上添加共享方法
Animal.prototype.walk = function() {
  console.log(`${this.name} is walking with ${this.legs} legs.`);
};

// 3. 定义 Dog 构造函数（继承 Animal）
function Dog(name, legs, breed) {
  Animal.call(this, name, legs); // 继承 Animal 的属性
  this.breed = breed; // 实例属性：品种
}
// 设置 Dog.prototype 继承 Animal.prototype
Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog;
// 在 Dog.prototype 上添加共享方法
Dog.prototype.bark = function() {
  console.log(`${this.name} is barking.`);
};

// 4. 创建一个 Dog 实例
const d = new Dog('Rex', 4, 'Labrador');
```
```
是的，你完全说对了！在ES5及更早的版本中，要实现继承确实需要手动修改原型链，过程比较繁琐且容易出错。不过，从ES6开始，JavaScript提供了更优雅的语法——class 和 extends 关键字，让继承的实现变得像传统面向对象语言一样直观。



### 1. 变量与数据类型
```javascript
// 变量声明
let name = "张三";    // 可重新赋值
const age = 25;       // 不可重新赋值
var oldWay = "不推荐"; // 旧方式，避免使用

// 基本数据类型
const str = "字符串";
const num = 42;
const bool = true;
const nul = null;     // 空值
const undef = undefined; // 未定义
const sym = Symbol(); // 唯一标识符，ES6新特性
```

### 2. 运算符
```javascript
// 算术运算符
+ - * / % ** 

// **: 2**3=8 指数运算符

// 比较运算符
==  // 相等（会类型转换）
=== // 严格相等（推荐）
!= !== > < >= <=

// 逻辑运算符
&& || ! 
```

### 3. 控制结构
```javascript
// 条件判断
if (condition) {
  // 代码
} else if (otherCondition) {
  // 代码
} else {
  // 代码
}

// 三元运算符
const result = condition ? value1 : value2;

// 循环
for (let i = 0; i < 5; i++) { }
while (condition) { }
do { } while (condition);


语法	遍历什么
for...of	遍历 值
for...in	遍历 键 / 下标 / 属性名
```

### 4. 函数
```javascript
// 函数声明
function add(a, b) {
  return a + b;
}

// 函数表达式，回调作为参数传入
const multiply = function(a, b) {
  return a * b;
};

// 箭头函数（ES6+）
const divide = (a, b) => a / b;
```

### 5. 对象与数组
```javascript
// 对象
const person = {
  name: "李四",
  age: 30,
  sayHello: function() {
    console.log("你好！");
  }
};

// 访问属性
person.name;        // 点表示法
person["age"];      // 方括号表示法

// 数组
const fruits = ["苹果", "香蕉", "橙子"];
fruits.push("葡萄"); // 添加元素
fruits.length;      // 数组长度
```

### 6.常用api

```
// ==================== Array ====================
const arr = [1, 2, 3];
arr.push(4);              // 尾部加一个元素，arr变成[1,2,3,4]
arr.pop();                // 删掉最后一个元素，返回被删的值
arr.unshift(0);           // 头部加一个元素
arr.shift();              // 删掉第一个元素，返回被删的值
arr.slice(1, 3);          // 截取下标1到3(前闭后开)，不改原数组，[1,2,3,4].slice(1,3) → [2,3]
arr.splice(1, 1, 9);      // 从下标1开始，删1个，插入9。会改原数组。[1,2,3].splice(1,1,9) → arr变成[1,9,3]
arr.map(x => x * 2);      // 对每个元素做操作，返回新数组。[1,2,3].map(x=>x*2) → [2,4,6]
arr.filter(x => x > 1);   // 过滤出满足条件的元素，返回新数组。[1,2,3].filter(x=>x>1) → [2,3]
arr.find(x => x === 2);   // 找第一个满足条件的元素，找不到返回undefined
arr.findIndex(x => x === 2); // 找第一个满足条件的下标，找不到返回-1
arr.includes(2);          // 数组是否包含某个值，返回true/false
arr.indexOf(2);           // 找某个值第一次出现的下标，找不到返回-1
arr.some(x => x > 2);     // 是否至少有一个元素满足条件，返回true/false
arr.every(x => x > 0);    // 是否所有元素都满足条件，返回true/false
arr.reduce((acc, cur) => acc + cur, 0); // 累加器，从初始值0开始，依次累加。[1,2,3] → 6
arr.sort((a, b) => a - b); // 排序，a-b升序，b-a降序。会改原数组
arr.join('-');             // 用分隔符把数组拼成字符串。[1,2,3].join('-') → '1-2-3'
arr.flat();               // 拍平一层嵌套。[[1,2],[3]].flat() → [1,2,3]
arr.concat([4, 5]);       // 合并数组，不改原数组。[1,2].concat([3,4]) → [1,2,3,4]
arr.reverse();            // 反转数组，会改原数组

// ==================== Object ====================
const obj = { a: 1, b: 2 };
Object.keys(obj);         // 拿所有key组成的数组 → ['a', 'b']
Object.values(obj);       // 拿所有value组成的数组 → [1, 2]
Object.entries(obj);      // 拿所有[key,value]对 → [['a',1], ['b',2]]
Object.assign({}, obj, { c: 3 }); // 合并对象，返回新对象 → {a:1, b:2, c:3}
obj.hasOwnProperty('a');  // 判断是否是自身属性(不含原型链上的)，返回true/false
Object.freeze(obj);       // 冻结对象，不能增删改任何属性
'a' in obj;               // 判断key是否存在(包括原型链)，返回true/false

// ==================== String ====================
const str = ' hello world ';
str.length;               // 字符串长度(包括空格)
str.trim();               // 去掉首尾空格 → 'hello world'
str.slice(1, 5);          // 截取下标1到5(前闭后开) → 'hell'
str.split(' ');           // 按空格切成数组 → ['', 'hello', 'world', '']
str.includes('world');    // 是否包含某子串，返回true/false
str.indexOf('o');         // 某字符第一次出现的位置，找不到返回-1
str.replace('world', 'js');   // 替换第一个匹配的 → ' hello js '
str.replaceAll(' ', '-');     // 替换所有匹配的
str.toUpperCase();        // 全部转大写
str.toLowerCase();        // 全部转小写
str.startsWith(' ');      // 是否以某字符开头
str.endsWith(' ');        // 是否以某字符结尾
str.charAt(1);            // 取某个位置的字符
str.repeat(2);            // 重复2次
str.padStart(20, '*');    // 用*在前面填充到20位
str.padEnd(20, '*');      // 用*在后面填充到20位

// ==================== Map ====================
// Map和普通对象的区别：key可以是任意类型(对象、数组、函数都行)，有序，有size
const map = new Map();
map.set('a', 1);          // 设置键值对
map.set({ x: 1 }, 'obj'); // key可以是对象
map.get('a');             // 根据key取值，没有返回undefined
map.has('a');             // 是否存在某个key
map.delete('a');          // 删除某个key
map.size;                 // 键值对数量
map.clear();              // 清空
// 遍历：for (const [key, value] of map) {}

// ==================== Set ====================
// Set：不重复的值的集合
const set = new Set([1, 2, 2, 3]); // 自动去重 → {1, 2, 3}
set.add(4);               // 添加值，已存在则忽略
set.has(2);               // 是否存在某值
set.delete(2);            // 删除某值
set.size;                 // 元素个数
set.clear();              // 清空
[...new Set([1,2,2,3])];  // 最常用：数组去重 → [1,2,3]
// 遍历：for (const item of set) {}

// ==================== Number / Math ====================
Number('123');            // 字符串转数字 → 123，转不了返回NaN
parseInt('12px');         // 从头解析整数 → 12，遇到非数字停止
parseFloat('3.14abc');    // 从头解析浮点数 → 3.14
(3.14159).toFixed(2);     // 保留2位小数，返回字符串 → '3.14'
Number.isNaN(NaN);        // 判断是不是NaN → true
Number.isInteger(3);      // 判断是不是整数 → true
Math.max(1, 5, 3);        // 最大值 → 5
Math.min(1, 5, 3);        // 最小值 → 1
Math.floor(3.9);          // 向下取整 → 3
Math.ceil(3.1);           // 向上取整 → 4
Math.round(3.5);          // 四舍五入 → 4
Math.random();            // 0到1之间的随机数(不含1)
Math.abs(-10);            // 绝对值 → 10
Math.pow(2, 3);           // 2的3次方 → 8

// ==================== Date ====================
const d = new Date();
d.getFullYear();          // 年 → 2025
d.getMonth();             // 月 → 0-11(注意0是1月!)
d.getDate();              // 日 → 1-31
d.getDay();               // 星期 → 0-6(0是周日)
d.getHours();             // 时
d.getMinutes();           // 分
d.getSeconds();           // 秒
d.getTime();              // 时间戳(毫秒)
Date.now();               // 当前时间戳(毫秒)

// ==================== JSON ====================
const json = JSON.stringify({ a: 1 }); // 对象转JSON字符串 → '{"a":1}'
JSON.parse(json);         // JSON字符串转对象 → { a: 1 }
// 注意：JSON.stringify 会丢失 undefined、函数、Symbol 类型的值

// ==================== Promise ====================
Promise.resolve(1).then(v => v);  // 创建一个成功的Promise
Promise.reject('err').catch(e => e); // 创建一个失败的Promise
// Promise.all([p1, p2])    → 全部成功才成功，一个失败就整体失败
// Promise.allSettled([p1, p2]) → 等全部结束，不管成功失败，返回每个结果
// Promise.race([p1, p2])   → 谁先结束用谁的结果(不管成功失败)
// Promise.any([p1, p2])    → 谁先成功用谁，全失败才失败

// ==================== 常用语法糖 ====================
const obj2 = { a: { b: 1 } };
obj2?.a?.b;               // 可选链：如果中间某层是null/undefined不会报错，返回undefined
null ?? '默认值';          // 空值合并：左边是null/undefined时用右边。和||的区别是：0和''不会被当成空
const arr2 = [...arr];    // 展开运算符：浅拷贝数组
const obj3 = { ...obj, c: 3 }; // 展开运算符：浅拷贝并合并对象
const { a } = obj;        // 对象解构：从obj里取出a
const [x, y] = arr;       // 数组解构：取前两个元素
const { a: renamed } = obj; // 解构时重命名：把a取出来叫renamed
const { a = 10 } = {};    // 解构时给默认值：如果没有a就用10
```

### 7. 异步编程基础
```javascript
// Promise
fetch('/api/data')
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error(error));

// async/await（更简洁）
async function getData() {
  try {
    const response = await fetch('/api/data');
    const data = await response.json();
    console.log(data);
  } catch (error) {
    console.error(error);
  }
}
```

### 8. DOM 操作
```javascript
// 选择元素
const element = document.querySelector('#myId');
const elements = document.querySelectorAll('.myClass');

// 修改内容
element.textContent = "新内容";
element.innerHTML = "<strong>HTML内容</strong>";

// 事件监听
element.addEventListener('click', function() {
  console.log('被点击了！');
});
```



## 函数专栏

Promise：



箭头函数：



回调函数：



异步函数：



## 异步专栏



