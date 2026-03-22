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
// Array
const arr = [1, 2, 3];
arr.push(4);          // 尾部加
arr.pop();            // 尾部删
arr.unshift(0);       // 头部加
arr.shift();          // 头部删
arr.slice(1, 3);      // 截取，不改原数组，[a, b, c, d].slice(1, 3) // [b, c]
arr.splice(1, 1, 9);  // 删除/替换/插入，���原数组
arr.map(x => x * 2);  // 映射新数组
arr.filter(x => x > 1); // 过滤
arr.find(x => x === 2); // 找第一个满足条件的值
arr.findIndex(x => x === 2); // 找下标
arr.includes(2);      // 是否包含
arr.indexOf(2);       // 元素下标
arr.some(x => x > 2); // 是否至少一个满足
arr.every(x => x > 0); // 是否全部满足
arr.reduce((a, b) => a + b, 0); // 累加/聚合
arr.sort((a, b) => a - b); // 排序
arr.join('-');        // 转字符串
arr.flat();           // 拍平一层

// Object
const obj = { a: 1, b: 2 };
Object.keys(obj);     // ['a', 'b']
Object.values(obj);   // [1, 2]
Object.entries(obj);  // [['a',1], ['b',2]]
Object.assign({}, obj, { c: 3 }); // 合并对象
obj.hasOwnProperty('a'); // 是否有自身属性

// String
const str = ' hello world ';
str.length;           // 长度
str.trim();           // 去首尾空格
str.slice(1, 5);      // 截取
str.split(' ');       // 切数组
str.includes('world'); // 是否包含
str.indexOf('o');     // 查位置
str.replace('world', 'js'); // 替换
str.toUpperCase();    // 大写
str.toLowerCase();    // 小写
str.startsWith(' ');  // 是否以某字符开头
str.endsWith(' ');    // 是否以某字符结尾

// Map
const map = new Map();
map.set('a', 1);      // 设置
map.get('a');         // 取值
map.has('a');         // 是否存在
map.delete('a');      // 删除
map.size;             // 长度
map.clear();          // 清空

// Set
const set = new Set([1, 2, 2, 3]);
set.add(4);           // 添加
set.has(2);           // 是否存在
set.delete(2);        // 删除
set.size;             // 长度
set.clear();          // 清空
[...new Set([1,2,2,3])]; // 数组去重

// Number / Math
Number('123');        // 转数字
parseInt('12px');     // 转整数
parseFloat('3.14');   // 转浮点数
(3.14159).toFixed(2); // 保留小数
Math.max(1, 5, 3);    // 最大值
Math.min(1, 5, 3);    // 最小值
Math.floor(3.9);      // 向下取整
Math.ceil(3.1);       // 向上取整
Math.round(3.5);      // 四舍五入
Math.random();        // 随机数
Math.abs(-10);        // 绝对值

// JSON
const json = JSON.stringify({ a: 1 }); // 对象转字符串
JSON.parse(json);     // 字符串转对象

// 常用语法
const obj2 = { a: { b: 1 } };
obj2?.a?.b;           // 可选链，防止报错
null ?? '默认值';      // 空值合并
const arr2 = [...arr]; // 展开
const obj3 = { ...obj, c: 3 }; // 对象展开
const { a } = obj;    // 解构
const [x, y] = arr;   // 数组解构

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



