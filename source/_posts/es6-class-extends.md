---
title: ES6 Class Extends
date: 2018-01-09 24:24:00
tags:
  - es6
---

### 简介

Class 可以通过`extends`关键字实现继承。

### 代码分析

```js
class ColorPoint extends Point {
  constructor(x, y, color) {
    // 子类必须在constructor方法中调用super方法，
    // 否则新建实例时会报错。
    // 这是因为子类没有自己的this对象，而是继承父类的this对象，然后对其进行加工。
    super(x, y); // 调用父类的constructor(x, y)
    this.color = color;
  }

  toString() {
    return this.color + ' ' + super.toString(); // 调用父类的toString()
  }
}
```
