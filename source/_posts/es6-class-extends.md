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
// 通过 extends 实现继承
// 只要是一个有 prototype 属性的函数，就能被继承；
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

### `__proto__` 属性 和 prototype 属性

```js
// 类的继承的实现方式
class A { }
class B { }
// B 的实例继承 A 的实例
Object.setPrototypeOf(B.prototype, A.prototype);
// B 的实例继承 A 的静态属性
Object.setPrototypeOf(B, A);

const b = new B();
```

```js
// setPrototypeOf 的实现
Object.setPrototypeOf = function (obj, proto) {
  obj.__proto__ = proto;
  return obj;
};
```

```js
// 1. ES5: 每一个对象都有__proto__属性，
// 指向对应的构造函数的prototype属性。
// 2. ES6: 子类的__proto__属性，表示构造函数的继承，总是指向父类。
B.__proto__ === A;
// ES6: 子类 prototype 属性的 __proto__ 属性，
// 表示方法的继承，总是指向父类的 prototype 属性。
B.prototype.__proto__ === A.prototype;
```

### 原生构造函数的继承

原生构造函数是指语言内置的构造函数，通常用来生成数据结构。

- Boolean()
- Number()
- String()
- Array()
- Date()
- Function()
- RegExp()
- Error()
- Object()

以前，原生构造函数，无法被继承，
以为原生构造函数的 this 无法绑定，导致拿不到内部属性；

ES5 是先新建子类的实例对象 this，
再将父类的属性添加到子类上，
由于父类的内部属性无法获取，导致无法继承原生的构造函数；

ES6 允许继承原生构造函数定义子类，
因为 ES6 是先新建父类的实例对象this，
然后再用子类的构造函数修饰this，
使得父类的所有行为都可以继承。

```js
class MyArray extends Array {
  constructor(...args) {
    super(...args);
  }
}

var arr = new MyArray();
arr[0] = 12;
arr.length // 1

arr.length = 0;
arr[0] // undefined
```

extends关键字不仅可以用来继承类，还可以用来继承原生的构造函数。
因此可以在原生数据结构的基础上，定义自己的数据结构。

### 参考文档

- [Class 的继承](http://es6.ruanyifeng.com/#docs/class-extends)
