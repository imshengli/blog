---
title: JS Object
date: 2018-01-18 14:33:10
categories: JavaScript
tags:
  - js
---

## 总结

JavaScript 原生对象：`Object`。
JavaScript 的所有其他对象都继承自`Object`对象。

Object 对象的方法：

* Object 本身的方法（静态方法）：直接定义在 Object 对象的方法；
* Object 的实例方法：定义在 Object 原型对象 Object.prototype 上的方法；

> 实例继承构造函数原型对象上的属性和方法。

## 语法

**基本形式**

```js
var obj = {
  foo: 'Hello',
  bar: 'World'
};
```

对象的属性有下面几个描述符：

* configurable：是否可配置，只有当此描述符为true时，该描述符才能被改变，并且该属性才能被删除；
* enumerable：当且仅当该属性为true时，该属性才能够出现在对象的枚举属性中。

* value：该属性对应的值。
* writable：当且仅当该属性的writable为true时，value才能被赋值运算符改变。

* get：一个给属性提供 getter 的方法。
* set：一个给属性提供 setter 的方法。

**delete**

delete命令只能删除对象本身的属性，无法删除继承的属性。

**in 运算符**

用于检查对象是否包含某个属性（继承的也会返回 true）。

**for...in**

用来遍历一个对象的全部属性。

它遍历的是对象所有可遍历（enumerable）的属性，会跳过不可遍历的属性。
它不仅遍历对象自身的属性，还遍历继承的属性。

**with**

操作同一个对象的多个属性时，提供一些书写的方便。

如果with区块内部有变量的赋值操作，
必须是当前对象已经存在的属性，
否则会创造一个当前作用域的全局变量。
因此**不建议采用**。

```js
var obj = {
  p1: 1,
  p2: 2,
};
with (obj) {
  p1 = 4;
  p2 = 5;
}
// 等同于
obj.p1 = 4;
obj.p2 = 5;
```

## 方法

### `Object()`

Object本身是一个函数，将任意值转为对象。
常用于保证某个值一定是对象。

```js
// 参数为无，undefined，null 时
// 返回空对象
var obj = Object();
var obj = Object(undefined);
var obj = Object(null);

// 如果参数是原始类型的值，
// Object方法将其转为对应的包装对象的实例
var obj = Object('foo');
obj instanceof Object; // true
obj instanceof String; // true

// 如果Object方法的参数是一个对象，
// 它总是返回该对象，即不用转换。
var fn = function () {};
var obj = Object(fn); // 返回原函数
obj === fn; // true
```

### Object 构造函数

即前面可以使用new命令。

```js
var obj = new Object();
// 等同于
var obj = {};
```

### Object 的静态方法

**Object.keys()**

参数是一个对象，返回一个数组。
该数组的成员都是该对象自身的（而不是继承的）、可枚举的所有属性名。

**Object.getOwnPropertyNames()**

参数是一个对象，返回一个数组。
该数组的成员都是该对象自身的所有属性名。（包括不可枚举的）

**Object.getOwnPropertyDescriptor()**

获取某个属性的描述对象。

**Object.defineProperty()**

通过描述对象，定义某个属性。

**Object.defineProperties()**

通过描述对象，定义多个属性。

**Object.preventExtensions()**

防止对象扩展。

**Object.isExtensible()**

判断对象是否可扩展。

**Object.seal()**

禁止对象配置。

**Object.isSealed()**

判断一个对象是否可配置。

**Object.freeze()**

冻结一个对象。

**Object.isFrozen()**

判断一个对象是否被冻结。

**Object.create()**

该方法可以指定原型对象和属性，返回一个新的对象。

**Object.getPrototypeOf()**

获取对象的Prototype对象。

### 实例方法

**Object.prototype.valueOf()**

返回当前对象对应的值。
主要用途是：JavaScript 自动类型转换时会默认调用这个方法。

**Object.prototype.toString()**

返回当前对象对应的字符串形式。
对于一个对象调用`toString`方法，会返回字符串`[object Object]`，该字符串说明对象的类型。

数组、字符串、函数、Date 对象都分别部署了自定义的toString方法，
覆盖了`Object.prototype.toString`方法。
为了得到类型字符串，
最好直接使用`Object.prototype.toString`方法。
通过函数的`call`方法，可以在任意值上调用这个方法，帮助我们判断这个值的类型。

```js
Object.prototype.toString.call(value);
```

**Object.prototype.toLocaleString()**

返回当前对象对应的本地字符串形式。
目前，主要有三个对象自定义了toLocaleString方法。

```js
Array.prototype.toLocaleString();
Number.prototype.toLocaleString();
Date.prototype.toLocaleString();
```

**Object.prototype.hasOwnProperty()**

判断某个属性是否为当前对象自身的属性，
还是继承自原型对象的属性。

**Object.prototype.isPrototypeOf()**

判断当前对象是否为另一个对象的原型。

**Object.prototype.propertyIsEnumerable()**

判断某个属性是否可枚举。
