---
title: ES6 Generator
date: 2018-01-23 09:34:00
categories: JavaScript
tags:
  - es6
---

## 总结

Generator 函数是 ES6 提供的一种异步编程解决方案。

Generator 函数：
语法上： 状态机，封装多个内部状态；返回遍历器对象，遍历器对象生成函数；

Generator 函数的一般形式：

```js
function* helloWorldGenerator() {
  yield 'hello';
  yield 'world';
  return 'ending';
}
// 返回一个遍历器对象
var hw = helloWorldGenerator();
hw.next();
// { value: 'hello', done: false }
hw.next();
// { value: 'world', done: false }
hw.next();
// { value: 'ending', done: true }
```
