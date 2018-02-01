---
title: JS 面向对象编程
date: 2018-01-18 14:37:10
categories: JavaScript
tags:
  - js
---

## 总结

JavaScript 语言具有很强的面向对象编程能力。

JavaScript中的对象体系是怎么样的？
构造函数的相关细节？
创建对象的相关细节？

## 概述

面向对象编程（Object Oriented Programming，缩写为 OOP）；

**对象是什么**

1）对象是单个实物的抽象。
2）对象是一个容器，封装了属性（property）和方法（method）。

## 构造函数

JavaScript 语言的对象体系，不是基于“类”的，
而是基于构造函数（constructor）和原型链（prototype）。

JavaScript 语言使用构造函数（constructor）作为对象的模板。
所谓”构造函数”，就是专门用来生成对象的函数。
它提供模板，描述对象的基本结构。
一个构造函数，可以生成多个对象，这些对象都有相同的结构。

```js
// 函数体内部使用了this关键字，代表了所要生成的对象实例。
// 生成对象的时候，必须用new命令，调用Vehicle函数。
var Vehicle = function () {
  this.price = 1000;
};
var v = new Vehicle();
// 对象从构造函数Vehicle继承了price属性
v.price; // 1000
// new命令本身就可以执行构造函数，
// 所以后面的构造函数可以不带括号
var v = new Vehicle;
// 函数被当成普通函数，函数中 this 代表了全局对象
var v = Vehicle();
// 如何保证只能使用 new 调用？
// 一种保证方式是：使用严格模式，
// 在严格模式中，函数内部的this不能指向全局对象，
// 默认等于undefined，
var Vehicle = function () {
  'use strict';
  this.price = 1000;
};
// 另一种解决方式是在构造函数内部判断是否使用new命令
var Vehicle = function () {
  if (!(this instanceof Vehicle)) {
    return new Vehicle();
  }
  this.price = 1000;
};
```

## new 命令

**new 命令的原理**

使用new命令时，它后面的函数调用就不是正常的调用，而是依次执行下面的步骤。

> 创建一个空对象，作为将要返回的对象实例；
> 将这个空对象的原型，指向构造函数的prototype属性；
> 将这个空对象赋值给函数内部的this关键字；
> 开始执行构造函数内部的代码；

构造函数之所以叫“构造函数”，
就是说这个函数的目的，就是操作一个空对象（即this对象），
将其“构造”为需要的样子。

如果构造函数内部有return语句，
而且return后面跟着一个对象，
new命令会返回return语句指定的对象；
否则，就会不管return语句，返回this对象。

```js
var Vehicle = function () {
  this.price = 1000;
  return 1000;
};
(new Vehicle()) === 1000
// false
```

如果对普通函数（内部没有this关键字的函数）使用new命令，
则会返回一个空对象。

```js
function getMessage() {
  // 被 new 忽略
  return 'this is a message';
}
var msg = new getMessage();
msg // {}
typeof msg // "object"
```

new命令简化的内部流程，可以用下面的代码表示。

```js
function _new(/* 构造函数 */ constructor, /* 构造函数参数 */ param1) {
  // 将 arguments 对象转为数组
  var args = [].slice.call(arguments);
  // 取出构造函数
  var constructor = args.shift();
  // 创建一个空对象，继承构造函数的 prototype 属性
  var context = Object.create(constructor.prototype);
  // 执行构造函数
  var result = constructor.apply(context, args);
  // 如果返回结果是对象，就直接返回，否则返回 context 对象
  return (typeof result === 'object' && result != null) ? result : context;
}
// 实例
var actor = _new(Person, '张三', 28);
```

**new.target**

函数内部可以使用`new.target`属性。
如果当前函数是new命令调用，new.target指向当前函数，
否则为undefined。

```js
function f() {
  // 使用这个属性，可以判断函数调用的时候，是否使用new命令。
  if (!new.target) {
    throw new Error('请使用 new 命令调用！');
  }
  console.log(new.target === f);
}
f() // false
new f() // true
```

**使用 Object.create() 创建实例对象**

如何以实例对象作为模板，来生成新的实例？

```js
var person1 = {
  name: '张三',
  age: 38,
  greeting: function() {
    console.log('Hi! I\'m ' + this.name + '.');
  }
};
var person2 = Object.create(person1);
person2.name // 张三
person2.greeting() // Hi! I'm 张三.
```

## 对象与继承

对象属性：自身属性和继承属性；

**Object.getOwnPropertyNames()**

参数：对象；
返回：数组，对象本身属性的键名，不包含继承的属性；

```js
Object.getOwnPropertyNames(Date);
```

**Object.keys**

参数：对象；
返回：数组，只获取那些可以枚举的属性；

```js
Object.keys(Date) // []
```

**Object.prototype.hasOwnProperty()**

对象实例的hasOwnProperty方法返回一个布尔值，
用于判断某个属性定义在对象自身，还是定义在原型链上。

```js
Date.hasOwnProperty('length')
// true
Date.hasOwnProperty('toString')
// false
```

**in 运算符和 for…in 循环**

in运算符返回一个布尔值，表示一个对象是否具有某个属性。
属性包括：自身的、继承的。

```js
'length' in Date // true
'toString' in Date // true
```

获得对象的所有可枚举属性（不管是自身的还是继承的），
可以使用`for...in`循环。

```js
var o1 = {p1: 123};
var o2 = Object.create(o1, {
  p2: { value: "abc", enumerable: true }
});
for (p in o2) {console.info(p);}
// p2
// p1
```

**对象的拷贝**

如果要拷贝一个对象，需要做到下面两件事情。

* 确保拷贝后的对象，与原对象具有同样的prototype原型对象。
* 确保拷贝后的对象，与原对象具有同样的属性。


## prototype 对象

prototype 对象作用：
为了解决对象的属性和方法共享问题，引入了“原型对象”。
定义所有实例对象共享的属性和方法。

## 参考链接

- [构造函数与 new 命令](http://javascript.ruanyifeng.com/oop/basic.html)
