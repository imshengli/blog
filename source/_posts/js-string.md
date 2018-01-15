---
title: JS 字符串
date: 2018-01-15 19:04:10
categories: JavaScript
tags:
  - js
---

## 总结

JavaScript 的字符以 `UTF-16` 格式存储，
每个字符占两个字节；
但对于大端字符，每个字符长度为2，占四个字节；
`\`是 JavaScript 中的特殊处理，注意特殊处理；
字符数组长度和单个字符无法更改；

## 简介

如果长字符串必须分成多行，可以在每一行的尾部使用反斜杠。

```js
// 反斜杠的后面必须是换行符，而不能有其他字符（比如空格）
var longString = 'Long \
long \
long \
string';
longString
// "Long long long string"
```

## 转义

反斜杠（\）在字符串内有特殊含义，
用来表示一些特殊字符，所以又称为转义符。

> \0 ：null（\u0000）
> \b ：后退键（\u0008）
> \f ：换页符（\u000C）
> \n ：换行符（\u000A）
> \r ：回车键（\u000D）
> \t ：制表符（\u0009）
> \v ：垂直制表符（\u000B）
> \' ：单引号（\u0027）
> \" ：双引号（\u0022）
> \\ ：反斜杠（\u005C）

如果在非特殊字符前面使用反斜杠，则反斜杠会被省略。

```js
'\a'; // "a"
```

特殊用法：

1）`\HHH`：紧跟三个八进制数（000到377），代表一个字符。

HHH对应该字符的 Unicode 码点，比如`\251`表示版权符号。
显然，这种方法只能输出256种字符。

2）`\xHH`：紧跟两个十六进制数（00到FF），代表一个字符。

HH对应该字符的 Unicode 码点，比如`\xA9`表示版权符号。
这种方法也只能输出256种字符。

3）`\uXXXX`：紧跟四个十六进制数（0000到FFFF），代表一个字符。

HHHH对应该字符的 Unicode 码点，比如`\u00A9`表示版权符号。

## 字符串与数组

**无法改变字符串之中的单个字符。**
**字符串 length 属性也是无法改变的**

```js
var s = 'hello';
delete s[0];
s // "hello"
s[1] = 'a';
s // "hello"
s[5] = '!';
s // "hello"
```

## 字符集

JavaScript 使用 `Unicode` 字符集。
JavaScript 引擎内部，所有字符都用 `Unicode` 表示。

JavaScript 允许直接在程序中使用 Unicode 码点表示字符，
即将字符写成`\uxxxx`的形式，其中`xxxx`代表该字符的 Unicode 码点。
比如，`\u00A9`代表版权符号。

```js
var f\u006F\u006F = 'abc';
foo // "abc"
```

> UTF-16 是 Unicode 的实现；
> 每个字符在 JavaScript 内部都是以16位（即2个字节）的 UTF-16 格式储存。
> 也就是说，JavaScript 的单位字符长度固定为16位长度，即2个字节。
> 但是，UTF-16 有两种长度：
> 对于码点在U+0000到U+FFFF之间的字符，长度为16位（即2个字节）；
> 对于码点在U+10000到U+10FFFF之间的字符，长度为32位（即4个字节），
> 而且前两个字节在0xD800到0xDBFF之间，后两个字节在0xDC00到0xDFFF之间。
> 举例来说，码点`U+1D306`对应的字符为`𝌆`，它写成 UTF-16 就是`0xD834 0xDF06`。

JavaScript 对 UTF-16 的支持是不完整的，
由于历史原因，只支持两字节的字符，不支持四字节的字符。
这是因为 JavaScript 第一版发布的时候，
Unicode 的码点只编到`U+FFFF`，因此两字节足够表示了。
后来，Unicode 纳入的字符越来越多，出现了四字节的编码。
但是，JavaScript 的标准此时已经定型了，
统一将字符长度限制在两字节，导致无法识别四字节的字符。
四字节字符`𝌆`，浏览器会正确识别这是一个字符，
但是 JavaScript 无法识别，会认为这是两个字符。

## Base64 编码

有时，文本里面包含一些不可打印的符号，
比如 ASCII 码0到31的符号都无法打印出来，
这时可以使用 Base64 编码，将它们转成可以打印的字符。
另一个场景是，有时需要以文本格式传递二进制数据，
那么也可以使用 Base64 编码。

> 所谓 Base64 就是一种编码方法，
> 可以将任意值转成 0～9、A～Z、a-z、+和/这64个字符组成的可打印字符。
> 使用它的主要目的，不是为了加密，
> 而是为了不出现特殊字符，简化程序的处理。

> `btoa()`：任意值转为 Base64 编码
> `atob()`：Base64 编码转为原来的值

```js
var string = 'Hello World!';
btoa(string) // "SGVsbG8gV29ybGQh"
atob('SGVsbG8gV29ybGQh') // "Hello World!"

// 注意，这两个方法不适合非 ASCII 码的字符，会报错。
btoa('你好') // 报错
// 要将非 ASCII 码字符转为 Base64 编码，
// 必须中间插入一个转码环节，再使用这两个方法。
function b64Encode(str) {
  return btoa(encodeURIComponent(str));
}
function b64Decode(str) {
  return decodeURIComponent(atob(str));
}
b64Encode('你好') // "JUU0JUJEJUEwJUU1JUE1JUJE"
b64Decode('JUU0JUJEJUEwJUU1JUE1JUJE') // "你好"
```

## 参考链接

- [阮一峰，字符串](http://javascript.ruanyifeng.com/grammar/string.html)
- [JavaScript: 详解Base64编码和解码](https://my.oschina.net/goal/blog/201032)
