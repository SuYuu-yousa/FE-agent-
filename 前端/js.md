# JavaScript 基础知识简明指南

## 核心概念

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

### 6. 数组常用方法
```javascript
const numbers = [1, 2, 3, 4, 5];

numbers.map(x => x * 2);     // [2,4,6,8,10]
numbers.filter(x => x > 2);  // [3,4,5]
numbers.find(x => x > 3);    // 4,找出第一个符合条件的元素，找不到返回undefined
numbers.includes(3);         // true
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


