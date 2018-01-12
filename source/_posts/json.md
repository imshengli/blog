---
title: JSON
date: 2018-01-11 17:55:10
tags:
  - js
---

### 简介

JSON 是一种语法，用来序列化对象、数组、数值、字符串、布尔值和 null 。

### `JSON.stringify()`

将一个 JavaScript 值(对象或者数组)转换为一个 JSON 字符串，

```js
// replacer
// 如果该参数是一个函数，则在序列化过程中，
// 被序列化的值的每个属性都会经过该函数的转换和处理；
// 如果该参数是一个数组，则只有包含在这个数组中的属性名
// 才会被序列化到最终的 JSON 字符串中；
// 如果该参数为null或者未提供，则对象所有的属性都会被序列化；
// space
// 指定缩进用的空白字符串，用于美化输出（pretty-print）；
JSON.stringify(value[, replacer [, space]]);
```

- 非数组对象的属性不能保证以特定的顺序出现在序列化后的字符串中。
- 布尔值、数字、字符串的包装对象在序列化过程中会自动转换成对应的原始值。
- undefined、任意的函数以及 symbol 值，在序列化过程中会被忽略（出现在非数组对象的属性值中时）或者被转换成 null（出现在数组中时）。
- 所有以 symbol 为属性键的属性都会被完全忽略掉，即便 replacer 参数中强制指定包含了它们。
- 不可枚举的属性会被忽略；

```js
JSON.stringify({});                        // '{}'
JSON.stringify(true);                      // 'true'
JSON.stringify("foo");                     // '"foo"'
JSON.stringify([1, "false", false]);       // '[1,"false",false]'
JSON.stringify({ x: 5 });                  // '{"x":5}'

JSON.stringify({x: 5, y: 6});
// "{"x":5,"y":6}"

JSON.stringify([new Number(1), new String("false"), new Boolean(false)]);
// '[1,"false",false]'

JSON.stringify({x: undefined, y: Object, z: Symbol("")});
// '{}'

JSON.stringify([undefined, Object, Symbol("")]);
// '[null,null,null]'

JSON.stringify({[Symbol("foo")]: "foo"});
// '{}'

JSON.stringify({[Symbol.for("foo")]: "foo"}, [Symbol.for("foo")]);
// '{}'
```

**replacer**

```js
function replacer(key, value) {
  if (typeof value === "string") {
    return undefined;
  }
  return value;
}

var foo = {
  foundation: "Mozilla",
  model: "box",
  week: 45,
  transport: "car",
  month: 7
};
var jsonString = JSON.stringify(foo, replacer);
// {"week":45,"month":7}

JSON.stringify(foo, ['week', 'month']);
// '{"week":45,"month":7}', 只保留“week”和“month”属性值。
```

**toJSON 方法**

如果一个被序列化的对象拥有 toJSON 方法，
那么该 toJSON 方法就会覆盖该对象默认的序列化行为：
不是那个对象被序列化，
而是调用 toJSON 方法后的返回值会被序列化，例如：

```js
var obj = {
  foo: 'foo',
  toJSON: function () {
    return 'bar';
  }
};
JSON.stringify(obj);      // '"bar"'
JSON.stringify({x: obj}); // '{"x":"bar"}'
```

### `JSON.parse()`

用来解析JSON字符串，构造由字符串描述的JavaScript值或对象。

```js
// reviver
// 如果是一个函数，则规定了原始值如何被解析改造，在被返回之前。
JSON.parse(text[, reviver])
```

```js
JSON.parse('{"p": 5}', function (k, v) {
    if(k === '') return v;     // 如果到了最顶层，则直接返回属性值，
    return v * 2;              // 否则将属性值变为原来的 2 倍。
});
```

### 参考链接

- [MDN JSON.stringify()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)
