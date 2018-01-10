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
// 父类
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

// 子类
class ColorPoint extends Point {
  // 任何子类都必须有 constructor 方法
  /** 默认
    * constructor(...args) {
    *   super(...args);
    * }
    */
  constructor(x, y, color) {
    // 关于super
    // 1. 子类必须在constructor方法中调用 super 方法，
    // 否则新建实例时会报错。
    // 这是因为子类没有自己的this对象，
    // 而是继承父类的this对象，然后对其进行加工。
    // 2. super，既可以当作函数使用，也可以当作对象使用;
    // 当做函数时，代表 Point 的构造函数，但内部 this 指向子类 ColorPoint，
    // 只能在构造函数中使用，返回的是子类的实例，
    // 相当于 Point.prototype.constructor.call(this);
    // 当做对象时，指向父类的原型对象，
    // 在静态方法中，指向父类；
    // 定义在父类实例上的方法或属性，无法通过 super 调用；
    // ES6规定，通过super调用父类的方法时，父类方法内部的this指向子类。
    // 3. 在静态方法中，super 指向父类，而不是父类的原型对象，
    // 4. 使用super时，必须指定super是作为函数还是对象使用；
    // 5. 由于对象总是继承其他对象的，
    // 所以可以在任意一个对象中，都可以使用super关键字。
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
