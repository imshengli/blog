---
title: (ES6) Class
date: 2018-01-09 10:24:00
tags:
  - es6
---

### 简介

生成实例的传统方法：通过构造函数；
ES6 引入了`class`，作为“对象的模板”；通过`class`，定义类。
基本上，ES6 的`class`可以看作只是一个语法糖，

### 代码分析

```js
const methodName = 'getArea';
function bar2(baz) {
  return this.snaf = baz;
}
const bar3 = Symbol('bar3');
const snaf3 = Symbol('snaf3');

// 通过`class`关键字定义类
class Point {
  // 类内部，默认是严格模式；所以不用写“use strict”

  // `constructor` 构造方法；
  // 一个类默认有 `constructor` 方法；
  // 默认返回实例对象，即 this，完全可以指定返回另外一个对象；
  constructor(x, y) {
    // this 代表 实例对象
    // this，它默认指向类的实例
    this.x = x;
    this.y = y;

    // this 的指向
    // 如果 printName 中使用 this，单独使用 printName 方法时，需要设置
    this.printName = this.printName.bind(this);
    // 还可以使用箭头函数
    this.printName = (name = 'there') => {
      this.print(`Hello ${name}`);
    };
    // 还有一种方法，使用 Proxy
    // 获取方法的时候，自动绑定 this
    // [Todos]

    // new.target 属性
    // 返回 new 命令作用于的那个构造函数
    // 可以用来确保构造函数只能用 new 命令调用
    // 可以写出不能独立使用、必须继承后才能使用的类。
    // 例如
    if (new.target === Point) {
      throw new Error('本类不能实例化');
    }
  }

  // 为了说明 this 的问题
  printName(name = 'there') {
    this.print(`Hello ${name}`);
  }
  print(text) {
    console.log(text);
  }

  // 定义类的方法
  // 其实是定义在类的`prototype`属性上面；
  // 类内部定义的所有方法，都是不可枚举的；Object.keys(Point.prototype)，这与ES5有差异；
  toString() {
    return '(' + this.x + ', ' + this.y + ')';
  }

  // 类的属性名，可以采用表达式。
  [methodName]() {
    // ...
  }

  // 私有方法1：通过命名加以区别
  _bar1(baz) {
    return this.snaf = baz;
  }
  // 私有方法2：将方法移出模块
  foo2(baz) {
    bar2.call(this, baz);
  }
  // 私有方法3：利用Symbol值的唯一性
  [bar3](baz) {
    return this[snaf3] = baz3;
  }

  // 私有属性，目前 ES6 还不提供

  // getter 和 setter
  // 在“类”的内部可以使用get和set关键字，
  // 对某个属性设置存值函数和取值函数，拦截该属性的存取行为。
  get prop() {
    return 'getter';
  }
  set prop(value) {
    console.log('setter: '+value);
  }
  // 存值函数和取值函数是设置在属性的 Descriptor 对象上的。
  // Descriptor 对象？
  // [Todos]

  // Generator 方法
  // 方法前加上星号
  * [Symbol.iterator]() {
    for (let arg of this.x) {
      yield arg;
    }
  }

  // 静态方法
  // 该方法不会被实例继承，而是直接通过类来调用
  // 父类的静态方法，可以被子类继承
  // 子类可以通过 super 对象，调用父类的静态方法
  static classMethod() {
    // this, 静态方法的 this 指的是类，而不是实例
    return 'hello';
  }

  // 静态属性和实例属性
  // 新提案
  // 实例属性
  state = {
    count: 0
  };
  // 静态属性
  static myStaticProp = 42;

}

typeof Point; // "function"
Point === Point.prototype.constructor // true

// name属性总是返回紧跟在class关键字后面的类名。
Point.name; // "Point"

// 静态属性
// ES6 明确规定，Class 内部只有静态方法，没有静态属性。
// 只能通过这种方式定义
Point.prop = 1;

// 类使用时，必须通过 new 调用，否则会报错；
// 这也是和普通构造函数不同的地方；
const p1 = new Point(5, 10); // 创建实例
const p2 = new Point(10, 10);

// 实例的属性：除非显式定义在其本身（this），否则都在定义在原型上；
p1.hasOwnProperty('x'); // true
p1.hasOwnProperty('toString'); // false
p1.__proto__.hasOwnProperty('toString') // true

// 所有实例共享一个原型；
p1.__proto__ === p2.__proto__; // true
```

### 一次添加多个方法

```js
// 类的方法都定义在prototype对象上面，
// 所以类的新方法可以添加在prototype对象上面。
// 一次向类添加多个方法
Object.assign(Point.prototype, {
    toString() {},
    toValue() {}
});
```

### class 表达式

```js
// 这个类的名字是MyClass
const MyClass = class Me {
  getClassName() {
    return Me.name;
  }
};
let inst = new MyClass();
inst.getClassName(); // Me
// Me 只在 Class 的内部代码可用，指代当前类。
Me.name // ReferenceError: Me is not defined
```

```js
// 简写
const MyClass = class { /* ... */ };
```

### class 不存在变量提升

```js
new Foo(); // ReferenceError
class Foo {};
```

```js
// 不存在变量提升的原因是：与继承有关，必须保证子类在父类之后定义。
{
  let Foo = class {};
  class Bar extends Foo {
  }
}
```
