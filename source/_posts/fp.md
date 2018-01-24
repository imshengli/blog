---
title: 函数式编程
date: 2018-01-17 13:09:10
categories: JavaScript
tags:
  - js
---

## 第一章 我们在做什么

通过一个例子，介绍了不可变的重要性。
例子中，如果中间值可变化，将得到不可预知的结果；

## 第二章 一等公民的函数

```js
var hi = function(name){
  return "Hi " + name;
};
var greeting = function(name) {
  return hi(name);
};
// 等价于
greeting = hi;
```

```js
var getServerStuff = function(callback){
  return ajaxCall(function(json){
    return callback(json);
  });
};

// 这行
return ajaxCall(function(json){
  return callback(json);
});
// 等价于这行
return ajaxCall(callback);

// 那么，重构下 getServerStuff
var getServerStuff = function(callback){
  return ajaxCall(callback);
};
// ...就等于
var getServerStuff = ajaxCall;
```

如果 httpGet 要改成可以抛出一个可能出现的 err 异常，
那我们还要回过头去把“胶水”函数也改了。

```js
httpGet('/post/2', function(json){
  return renderPost(json);
});
// 把整个应用里的所有 httpGet 调用都改成这样，可以传递 err 参数。
httpGet('/post/2', function(json, err){
  return renderPost(json, err);
});
```

命名也很重要，
不要把自己限定在特定的数据上（下面例子中是 articles）。

```js
// 只针对当前的博客
var validArticles = function(articles) {
  return articles.filter(function(article){
    return article !== null && article !== undefined;
  });
};
// 对未来的项目友好太多
var compact = function(xs) {
  return xs.filter(function(x) {
    return x !== null && x !== undefined;
  });
};
```

## 第三章 纯函数的好处

纯函数：
**纯函数是这样一种函数，即相同的输入，永远会得到相同的输出，而且没有任何可观察的副作用。**

```js
// 不纯的
var minimum = 21;
var checkAge = function(age) {
  return age >= minimum;
};
// 纯的
var checkAge = function(age) {
  var minimum = 21;
  return age >= minimum;
};
// 在不纯的版本中，checkAge 的结果将取决于 minimum 这个可变变量的值。
// 换句话说，它取决于系统状态（system state）；
// 这一点令人沮丧，因为它引入了外部的环境，从而增加了认知负荷（cognitive load）。
```

**副作用**

函数式编程的哲学就是假定副作用是造成不正当行为的主要原因。

**函数**

函数仅仅只是输入到输出的映射。
纯函数就是数学上的函数，而且是函数式编程的全部。

**追求“纯”的理由**

* 可缓存性（Cacheable）；

```js
var memoize = function(f) {
  var cache = {};
  return function() {
    var arg_str = JSON.stringify(arguments);
    cache[arg_str] = cache[arg_str] || f.apply(f, arguments);
    return cache[arg_str];
  };
};

var squareNumber  = memoize(function(x){ return x*x; });
squareNumber(4);
//=> 16
squareNumber(4); // 从缓存中读取输入值为 4 的结果
//=> 16
```

* 可移植性／自文档化（Portable / Self-Documenting）
* 可测试性（Testable）
* 合理性（Reasonable）

如果一段代码可以替换成它执行所得的结果，而且是在不改变整个程序行为的前提下替换的，那么我们就说这段代码是引用透明的。

* 并行代码

## 参考文档

- [JS 函数式编程指南](https://llh911001.gitbooks.io/mostly-adequate-guide-chinese/content/ch2.html)

