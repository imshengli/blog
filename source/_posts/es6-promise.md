---
title: ES6 Promise
date: 2018-01-14 13:20:10
categories: JavaScript
tags:
  - es6
---

## 总结

Promise 是一种异步解决方案，
在异步流程中，如果想在未来某一时刻执行某项操作，
可以通过回调或事件的方式处理，
但存在嵌套过深的场景。

通过Promise，可以通过链式链路进行操作，
简化了异步操作的控制；
ES6 提供了原生的解决方案。

如果自己完成一个Promise解决方案，
应该如何去处理呢？

## 简介

异步编程的一种解决方案。
ES6统一了用法，原生提供了Promise对象。

特点：

* 对象的状态不受外界影响；三种状态：Pending、fulfilled、rejected；
* 一旦状态改变，就不会再变，任何时候都可以得到这个结果；

**基本用法**

```js
// Promise对象是一个构造函数，用来生成Promise实例。
const promise = new Promise(function(resolve, reject) {
  // ... some code
  if (/* 异步操作成功 */){
    resolve(value);
  } else {
    reject(error);
  }
  // 会继续执行这行代码
  // 但最好不要这样做，在 then 里执行就好了；
  console.log(2);
});
// then 接受两个回调函数作为参数:
// 分别指定resolved状态和rejected状态的回调函数。
// 第二个方法可选
promise.then(function(value) {
  // success
}, function(error) {
  // failure
});
```

**异步加载图片**

```js
function loadImageAsync(url) {
  // 返回一个 Promise 实例
  return new Promise(function(resolve, reject) {
    const image = new Image();
    image.onload = function() {
      resolve(image);
    };
    image.onerror = function() {
      reject(new Error('Could not load image at ' + url));
    };
    image.src = url;
  });
}
```

## `Promise.prototype.then()`

添加状态改变时的回调函数。

`then`方法返回的是一个**新的Promise实例**。

如果返回的是一个 Promise 实例，
会等待该Promise对象的状态发生变化，才会被调用。

```js
loadImageAsync('https://imshengli.com/images/logo.png')
.then(image => {
  console.log(image);
  return image;
}).then(res => {
  console.log(res);
});
```

## `Promise.prototype.catch()`

是`.then(null, rejection)`的别名，
用于指定发生错误时的回调函数，
返回的还是 Promise 对象。

`then`方法指定的回调函数，如果运行中抛出错误，
也会被`catch`方法捕获。

Promise 对象的错误具有“冒泡”性质，
会一直向后传递，直到被捕获为止。

**建议总是使用`catch`方法，而不使用`then`方法的第二个参数。**

跟传统的try/catch代码块不同的是，
如果没有使用catch方法指定错误处理的回调函数，
Promise 对象**内部**抛出的错误不会传递到外层代码，
即不会有任何反应。

```js
// 不会退出进程、终止脚本执行，
const someAsyncThing = function() {
  return new Promise(function(resolve, reject) {
    // 下面一行会报错，因为x没有声明
    resolve(x + 2);
  });
};
someAsyncThing().then(function() {
  console.log('everything is great');
});
setTimeout(() => { console.log(123) }, 2000);
// Uncaught (in promise) ReferenceError: x is not defined
// 123
```

## `Promise.all()`

将多个 Promise 实例，包装成一个新的 Promise 实例。

```js
// 参数可以不是数组，但必须具有 Iterator 接口，
// 且返回的每个成员都是 Promise 实例；
// 如果不是，就会先调用的Promise.resolve方法，
// 将参数转为 Promise 实例。
const p = Promise.all([p1, p2, p3]);
// 都变成fulfilled，p的状态才会变成fulfilled，
// 有一个被rejected，p的状态就变成rejected。

// 如果 作为参数的 Promise 实例，
// 自己定义了catch方法，那么它一旦被rejected，
// 并不会触发Promise.all()的catch方法。
```

## `Promise.race()`

将多个 Promise 实例，包装成一个新的 Promise 实例。

```js
// 只要p1、p2、p3之中有一个实例率先改变状态，
// p的状态就跟着改变。
// 那个率先改变的 Promise 实例的返回值，
// 就传递给p的回调函数。
const p = Promise.race([p1, p2, p3]);
```

## `Promise.resolve()`

将现有对象转为 Promise 对象。

```js
// 将 jQuery 生成的deferred对象，转为一个新的 Promise 对象。
const jsPromise = Promise.resolve($.ajax('/whatever.json'));
```

参数：

* 参数是一个 Promise 实例： 直接返回；
* 参数是一个thenable对象：具有 then 方法，将这个对象转为 Promise 对象，然后就立即执行thenable对象的then方法；
* 参数不是具有then方法的对象，或根本就不是对象：返回一个新的 Promise 对象，状态为resolved；
* 不带有任何参数：直接返回一个resolved状态的 Promise 对象；

注意：

立即resolve的 Promise 对象，是在本轮“事件循环”（event loop）的结束时，
而不是在下一轮“事件循环”的开始时。
setTimeout(fn, 0)在下一轮“事件循环”开始时执行。

## `Promise.reject()`

返回一个新的 Promise 实例，该实例的状态为rejected。

```js
const p = Promise.reject('出错了');
// 等同于
const p = new Promise((resolve, reject) => reject('出错了'))

p.then(null, function (s) {
  console.log(s)
});
// 出错了
```

Promise.reject()方法的参数，
会原封不动地作为reject的理由，变成后续方法的参数。

```js
const thenable = {
  then(resolve, reject) {
    reject('出错了');
  }
};

Promise.reject(thenable)
.catch(e => {
  console.log(e === thenable)
})
// true
```

## `done()`

自行添加：
总是处于回调链的尾端，保证抛出任何可能出现的错误。

实现：

```js
Promise.prototype.done = function (onFulfilled, onRejected) {
  this.then(onFulfilled, onRejected)
    .catch(function (reason) {
      // 抛出一个全局错误
      setTimeout(() => { throw reason }, 0);
    });
};
```

## `finally()`

自行添加：
不管 Promise 对象最后状态如何，都会执行的操作。

实现：

```js
Promise.prototype.finally = function (callback) {
  let P = this.constructor;
  return this.then(
    value  => P.resolve(callback()).then(() => value),
    reason => P.resolve(callback()).then(() => { throw reason })
  );
};
```

## `Promise.try()`

自行添加：
不知道或者不想区分，函数f是同步函数还是异步操作，
但是想用 Promise 来处理它。

```js
// 缺点，就是如果f是同步函数，
// 那么它会在本轮事件循环的末尾执行。
Promise.resolve().then(f);
```

改进：

```js
const f = () => console.log('now');
(
  () => new Promise(
    resolve => resolve(f())
  )
)();
console.log('next');
// now
// next
```

## 参考文档

* [ES6 Promise by RuanYifeng](http://es6.ruanyifeng.com/#docs/promise)
* [q.js-Promise库的设计思路](https://div.io/topic/1351)
