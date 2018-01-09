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
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }
  // 静态方法，也会被子类继承
  static hello() {
    return 'hello';
  }
}

class ColorPoint extends Point {
  // 任何子类都必须有 constructor 方法
  /** 默认
    * constructor(...args) {
    *   super(...args);
    * }
    */
  constructor(x, y, color) {
    // 子类必须在constructor方法中调用super方法，
    // 否则新建实例时会报错。
    // 这是因为子类没有自己的this对象，而是继承父类的this对象，然后对其进行加工。
    super(x, y); // 调用父类的constructor(x, y)
    // 只有调用 super 方法后，才能用 this
    this.color = color;
  }

  toString() {
    return this.color + ' ' + super.toString(); // 调用父类的toString()
  }

}

// 可以使用这个方法判断，一个类是否继承了另一个类。
Object.getPrototypeOf(ColorPoint) === Point;

let cp = new ColorPoint(25, 8, 'green');

cp instanceof ColorPoint; // true
cp instanceof Point; // true
```
