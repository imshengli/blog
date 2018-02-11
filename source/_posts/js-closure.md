---
title: JS 闭包
date: 2018-02-08 13:21:10
categories: JavaScript
tags:
  - js
---

## 问题

变量作用域 -> 链式作用域

在外部如何读取内部的变量？

## 闭包

闭包就是能够读取其他函数内部变量的函数。

简单理解成"定义在一个函数内部的函数"。

在本质上，闭包就是将函数内部和函数外部连接起来的一座桥梁。

内部function会`close-over`外部function的变量。

## 用途

一个是前面提到的可以读取函数内部的变量；
另一个就是让这些变量的值始终保持在内存中。


## 注意点

内存消耗很大，所以不能滥用闭包；
闭包会在父函数外部，改变父函数内部变量的值。


## 参考

- Javascript编程精粹
- https://stackoverflow.com/questions/111102/how-do-javascript-closures-work
- https://www.cnblogs.com/frankfang/archive/2011/08/03/2125663.html
