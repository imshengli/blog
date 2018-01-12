---
title: ES6 Function
date: 2018-01-11 15:48:00
tags:
  - ES6
---

### 总结

- 参数默认值；
- rest 参数；
- name 属性;
- 严格模式;
- 箭头函数；
- 尾调用优化；

### 默认值

```js
// 0. 是 undefined 才会触发默认值
function log(x, y = 'World') {
  console.log(x, y);
}

// 1. 默认值是惰性求值
let x = 99;
function foo(p = x + 1) {
  console.log(p);
}
foo(); // 100
x = 100;
foo(); // 101

// 2. 与解构赋值默认值结合使用
function foo({x, y = 5}) {
  console.log(x, y);
}
foo({}); // undefined 5
foo({x: 1}); // 1 5
foo({x: 1, y: 2}); // 1 2
foo(); // TypeError: Cannot read property 'x' of undefined
// 通过提供默认值解决
// function foo({x, y = 5} = {}) {
//   console.log(x, y);
// }

// 3. 参数默认值的位置
// 通常情况下，定义了默认值的参数，应该是函数的尾参数。

// 4. length 属性
// length 含义：该函数预期传入的参数个数。
// 指定了默认值以后，函数的length属性，
// 将返回没有指定默认值的参数个数。
(function (a) {}).length; // 1
(function (a = 5) {}).length; // 0
(function (a, b, c = 5) {}).length; // 2
// 如果设置了默认值的参数不是尾参数，
// 那么length属性也不再计入后面的参数了。
(function (a = 0, b, c) {}).length; // 0
(function (a, b = 1, c) {}).length; // 1

// 5. 作用域
// 一旦设置了参数的默认值，函数进行声明初始化时，
// 参数会形成一个单独的作用域（context）,
// 等到初始化结束，这个作用域就会消失。
var x = 1;
function f(x, y = x) {
  console.log(y);
}
f(2); // 2
// 参数的默认值是一个函数，该函数的作用域也遵守这个规则。
let foo = 'outer';
function bar(func = () => foo) {
  let foo = 'inner';
  console.log(func());
}
bar(); // outer

// 6. 应用
// 利用参数默认值，可以指定某一个参数不得省略
function throwIfMissing() {
  throw new Error('Missing parameter');
}
function foo(mustBeProvided = throwIfMissing()) {
  return mustBeProvided;
}
foo(); // Error: Missing parameter
```

### rest 参数

ES6 引入 rest 参数（形式为`...变量名`），
用于获取函数的多余参数。
rest 参数搭配的变量是一个数组，该变量将多余的参数放入数组中。

```js
// arguments变量的写法
function sortNumbers() {
  return Array.prototype.slice.call(arguments).sort();
}
// rest参数的写法
const sortNumbers = (...numbers) => numbers.sort();

// rest 参数之后不能再有其他参数（即只能是最后一个参数）
// 报错
function f(a, ...b, c) {
  // ...
}
// 函数的length属性，不包括 rest 参数。
(function(a) {}).length;  // 1
(function(...a) {}).length;  // 0
(function(a, ...b) {}).length;  // 1
```

### 严格模式

从 ES5 开始，函数内部可以使用`use strict`设定为严格模式。

ES2016 做了一点修改，
规定只要函数参数使用了
默认值、解构赋值、或者扩展运算符，
那么函数内部就**不能显式设定为严格模式**，
否则会报错。

这样规定的原因是，
函数内部的严格模式，同时适用于函数体和函数参数。
但是，函数执行的时候，先执行函数参数，然后再执行函数体。
这样就有一个不合理的地方，只有从函数体之中，
才能知道参数是否应该以严格模式执行，
但是参数却应该先于函数体执行。

两种方法可以规避这种限制。

* 设定全局性的严格模式;
* 把函数包在一个无参数的立即执行函数里面;

### name 属性

返回该函数的函数名。

```js
// 如果将一个匿名函数赋值给一个变量，
// ES5 的name属性，会返回空字符串，
// 而 ES6 的name属性会返回实际的函数名。
var f = function () {};
// ES5
f.name; // ""
// ES6
f.name; // "f"

// Function构造函数返回的函数实例，name属性的值为anonymous。
(new Function).name; // "anonymous"

// bind返回的函数，name属性值会加上bound前缀。
function foo() {};
foo.bind({}).name; // "bound foo"
(function(){}).bind({}).name; // "bound "
```

### 箭头函数

```js
// 基本用法
var f = v => v;
// 等同于
var f = function(v) {
  return v;
};

// 如果箭头函数不需要参数或需要多个参数，
// 就使用一个圆括号代表参数部分。
var f = () => 5;
var sum = (num1, num2) => num1 + num2;

// 如果箭头函数的代码块部分多于一条语句，
// 就要使用大括号将它们括起来，
// 并且使用return语句返回。
var sum = (num1, num2) => {
  num1 += num2;
  return num1 + num2;
};
```

注意点：

* 函数体内的this对象，就是定义时所在的对象，而不是使用时所在的对象。
* 不可以当作构造函数，否则会抛出一个错误。
* 不可以使用arguments对象，该对象在函数体内不存在。如果要用，可以用 rest 参数代替。
* 不可以使用yield命令，因此箭头函数不能用作 Generator 函数；

**this 对象**

箭头函数没有自己的this，导致内部的this就是外层代码块的this。
正是因为它没有this，所以也就不能用作构造函数。

```js
function foo() {
  setTimeout(() => {
    console.log('id:', this.id);
  }, 100);
}
var id = 21;
foo.call({ id: 42 });
// id: 42

// ES6
function foo() {
  setTimeout(() => {
    console.log('id:', this.id);
  }, 100);
}
// ES5
function foo() {
  var _this = this;
  setTimeout(function () {
    console.log('id:', _this.id);
  }, 100);
}
```

以下三个变量在箭头函数之中也是不存在的，
指向外层函数的对应变量：

- arguments;
- super;
- new.target;

由于箭头函数没有自己的this，
所以当然也就不能用 `call()`、`apply()`、`bind()`
这些方法去改变this的指向。

**嵌套箭头函数**

```js
function add(x){
  return function(y){
    return y + x;
  };
}
const add = x => y => x + y;
add(1)(2); // 3

const has = p => o => o.hasOwnProperty(p);
const users = [
  { name: 'Qian', age: 27, pets : ['Bao'] },
  { name: 'Zeynep', age: 19, pets : ['Civelek'] },
  { name: 'Yael', age: 52 }
];
const result = users.filter(has('pets'));
```

### 尾调用优化

**尾调用（Tail Call）**

指某个函数的最后一步是调用另一个函数。

```js
function f(x) {
  return g(x);
}
```

**尾调用优化”（Tail call optimization）**

只保留内层函数的调用帧。

只有不再用到外层函数的内部变量，
内层函数的调用帧才会取代外层函数的调用帧，
否则就无法进行“尾调用优化”。

[Todos]
