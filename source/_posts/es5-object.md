---
title: ES5 Object
date: 2018-01-11 12:48:00
tags:
  - es5
  - js
---

### `Object.defineProperty()`

直接在一个对象上定义一个新属性，或者修改一个对象的现有属性， 并返回这个对象。

```js
Object.defineProperty(obj, prop, descriptor);
```

**属性描述符**

对象里目前存在的属性描述符有两种主要形式，描述符是两者中其中的一个：

- 数据描述符：一个具有值的属性，该值可能是可写的，也可能不是可写的；
- 存取描述符：由getter-setter函数对描述的属性；

两种描述符可选值：

- configurable：当且仅当该属性的 configurable 为 true 时，该属性描述符才能够被改变，同时该属性也能从对应的对象上被删除。默认为 false。
- enumerable：当且仅当该属性的enumerable为true时，该属性才能够出现在对象的枚举属性中。默认为 false。

数据描述符同时具有以下可选键值：

- value：该属性对应的值。可以是任何有效的 JavaScript 值（数值，对象，函数等）。默认为 undefined。
- writable：当且仅当该属性的 writable 为 true 时，value才能被赋值运算符改变。默认为 false。

存取描述符同时具有以下可选键值：

- get：一个给属性提供 getter 的方法，如果没有 getter 则为 undefined。该方法返回值被用作属性值。默认为 undefined。
- set：一个给属性提供 setter 的方法，如果没有 setter 则为 undefined。该方法将接受唯一参数，并将该参数的新值分配给该属性。默认为 undefined。

```js
// 在对象中添加一个属性与数据描述符的示例
Object.defineProperty({}, "a", {
  value : 37,
  writable : true,
  enumerable : true,
  configurable : true
});
```

### `Object.create()`

会使用指定的原型对象及其属性去创建一个新的对象。

```js
Object.create(proto[, propertiesObject]);
```

```js
// 参数：proto
// proto: null, 创建一个原型为null的空对象;
Object.create(null);
// 相当于 {}
Object.create(Object.prototype);
// 如果proto参数不是 null 或一个对象，则抛出一个 TypeError 异常。
Object.create(proto[, propertiesObject]);

// 参数：propertiesObject
// 省略了的属性特性默认为false, 所以属性p是不可写, 不可枚举, 不可配置的:
Object.create({}, {
  p: {
    value: 42
  }
});
// 创建一个可写的,可枚举的,可配置的属性p
Object.create({}, {
  p: {
    value: 42,
    writable: true,
    enumerable: true,
    configurable: true
  }
});

// 返回值：
// 在指定原型对象上添加新属性后的对象。
```

### 参考链接：

- [Object.defineProperty()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)
