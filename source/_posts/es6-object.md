---
title: ES6 Object
date: 2018-01-10 19:24:00
tags:
  - es6
---

### 总结

- 属性和方法简写；
- 表达式属性名和方法名；
- name 属性；
- Object.is();
- Object.assign();

### 代码分析

```js
const foo = 'bar';
const propKey = 'foo';
const key1 = Symbol('description');

const obj = {
  // 属性的简洁表示法
  foo,

  // 方法的简洁表示法
  // 等同于 hello: function ()...
  hello() {
    return "Hello!";
  },

  // 表达式属性名，在ES6中是允许的
  [propKey]: true,
  // 表达式也能定义方法名
  [propKey]() {
    return true;
  },
  // 注：属性名如果是对象，会转换为`[object Object]`

  // 方法的`name`属性
  // obj.sayName.name 返回 sayName
  sayName() {
    console.log('hello!');
  },
  // Symbol值：obj[key1].name // "[description]"
  // 返回这个 Symbol 值的描述。
  [key1]() {},

  // generate 方法
  * gen() {
    yield 'hello world';
  }
};
```

### `Object.is()`

用来比较两个值是否严格相等。

```js
Object.is('foo', 'foo'); // true
Object.is({}, {}); // false
Object.is(+0, -0); // false
Object.is(NaN, NaN); // true
```

```js
// ES5 中的实现
Object.defineProperty(Object, 'is', {
  value: function(x, y) {
    if (x === y) {
      // 针对+0 不等于 -0的情况
      return x !== 0 || 1 / x === 1 / y;
    }
    // 针对NaN的情况
    return x !== x && y !== y;
  },
  configurable: true,
  enumerable: false,
  writable: true
});
```

### `Object.assign()`

用于对象的合并，将源对象（source）的所有可枚举属性，复制到目标对象（target）。

```js
// 同名属性，会覆盖前面的；
Object.assign(target, source1, source2);

// 如果该参数不是对象，则会先转成对象，然后返回。
typeof Object.assign(2) // "object"

// 由于 undefined 和 null 无法转成对象，所以如果它们作为参数，就会报错。
Object.assign(undefined) // 报错
Object.assign(null) // 报错

// 如果 undefined 和 null 出现在非第一个参数上，不会报错；
let obj = {a: 1};
Object.assign(obj, undefined) === obj // true
Object.assign(obj, null) === obj // true
// 其他类型的值（即数值、字符串和布尔值）不在首参数，也不会报错。
// 但是，除了字符串会以数组形式，拷贝入目标对象，其他值都不会产生效果。
// 这是因为只有字符串的包装对象，会产生可枚举属性。
const v1 = 'abc';
const v2 = true;
const v3 = 10;
const obj = Object.assign({}, v1, v2, v3);
console.log(obj); // { "0": "a", "1": "b", "2": "c" }

// Object.assign拷贝的属性是有限制的，
// 只拷贝源对象的自身属性（不拷贝继承属性），
// 也不拷贝不可枚举的属性（enumerable: false）。
Object.assign({b: 'c'},
  Object.defineProperty({}, 'invisible', {
    enumerable: false,
    value: 'hello'
  })
);
// { b: 'c' }

// 属性名为 Symbol 值的属性，也会被Object.assign拷贝。
Object.assign({ a: 'b' }, { [Symbol('c')]: 'd' });
// { a: 'b', Symbol(c): 'd' }

```

**注意点**

浅拷贝：如果源对象某个属性的值是对象，那么目标对象拷贝得到的是这个对象的引用。

```js
const obj1 = {a: {b: 1}};
const obj2 = Object.assign({}, obj1);

obj1.a.b = 2;
obj2.a.b; // 2
```

同名属性的替换：一旦遇到同名属性，Object.assign的处理方法是替换，而不是添加。

```js
const target = { a: { b: 'c', d: 'e' } };
const source = { a: { b: 'hello' } };
Object.assign(target, source);
// { a: { b: 'hello' } }
```

数组的处理：Object.assign可以用来处理数组，但是会把数组视为对象。

```js
Object.assign([1, 2, 3], [4, 5]);
// [4, 5, 3]
```

取值函数的处理：Object.assign只能进行值的复制，如果要复制的值是一个取值函数，那么将求值后再复制。

```js
const source = {
  get foo() { return 1 }
};
const target = {};
Object.assign(target, source)
// { foo: 1 }
```

**常见用途**

为对象添加属性

```js
class Point {
  constructor(x, y) {
    Object.assign(this, {x, y});
  }
}
```

为对象添加方法

```js
Object.assign(SomeClass.prototype, {
  someMethod(arg1, arg2) {
    // ···
  },
  anotherMethod() {
    // ···
  }
});
```

克隆对象

```js
// 注：是浅拷贝，
// 只能克隆原始对象自身的值，不能克隆继承的值；
// [Todos] 如何克隆继承的值；
function clone(origin) {
  return Object.assign({}, origin);
}
```

合并多个对象

```js
const merge = (...sources) => Object.assign({}, ...sources);
```

为属性指定默认值

```js
const DEFAULTS = {
  logLevel: 0,
  outputFormat: 'html'
};
function processContent(options) {
  options = Object.assign({}, DEFAULTS, options);
  console.log(options);
  // ...
}
```
