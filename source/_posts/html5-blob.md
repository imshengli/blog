---
title: HTML5 Blob 对象
date: 2018-01-22 19:55:10
categories: HTML5
tags:
  - html
---

## 背景

本文主要总结HTML5中，关于Blob对象的相关知识。
Blob：Binary Large Object。

## 为什么要有Blob对象？

在[文件和二进制数据的操作](http://javascript.ruanyifeng.com/htmlapi/file.html)中，
讲JavaScript无法直接处理二进制数据，
而ECMAScript 5引入了Blob对象，允许直接操作二进制数据。

## 什么是Blob对象？

Blob对象，是一个代表着二进制数据的基本对象。

创建Blob对象：

* Blob构造函数；
* Blob对象的slice方法，将二进制数据按照字节分块，返回一个新的Blob对象。

Blob对象，有两个只读属性：

* size：二进制数据的大小，单位为字节。
* type：二进制数据的MIME类型，全部为小写，如果类型未知，则该值为空字符串。

## 扩展阅读

在Blob的基础上，又衍生出一系列相关的API，用来操作文件。

* File对象：负责处理那些以文件形式存在的二进制数据，也就是操作本地文件；
* FileList对象：File对象的网页表单接口；
* FileReader对象：负责将二进制数据读入内存内容；
* URL对象：用于对二进制数据生成URL。

## 参考链接

- [文件和二进制数据的操作](http://javascript.ruanyifeng.com/htmlapi/file.html)
