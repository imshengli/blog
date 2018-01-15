---
title: JS 正则表达式
date: 2018-01-14 16:54:10
categories: JavaScript
tags:
  - js
---

## 总结



## 简介

正则表达式，
是一种表达文本模式的方法。

```js
// 两种方式新建：
// 编译时（代码载入时）新建正则表达式
var regex = /xyz/i;
// 运行时新建正则表达式
var regex = new RegExp('xyz', 'i');

// 使用方式：
// 正则对象方法
regex.test(string);
regex.exec(string);
// 字符串对象方法 match
string.match(regex);
```

**正则对象的属性和方法**

```js
// 属性:

// 修饰符相关，返回一个布尔值，表示对应的修饰符是否设置。
// ignoreCase: 表示是否设置了i修饰符，该属性只读；
// global: 表示是否设置了g修饰符，该属性只读；
// multiline: 表示是否设置了m修饰符，该属性只读；
var r = /abc/igm;
r.ignoreCase // true
r.global // true
r.multiline // true
// lastIndex: 返回下一次开始搜索的位置。
//            该属性可读写，但是只在设置了g修饰符时有意义；
// source: 返回正则表达式的字符串形式（不包括反斜杠），该属性只读;
r.lastIndex // 0
r.source // "abc"


// 方法:

// test()
// 参数：用来与正则表达式匹配的字符串，
// 返回一个布尔值，表示当前模式是否能匹配参数字符串；
/cat/.test('cats and dogs') // true
// 如果正则表达式带有g修饰符，
// 则每一次test方法都从上一次结束的位置开始向后匹配。
// 但 lastIndex 属性只对同一个正则表达式有效
var count = 0;
// 会无限循环
while (/a/g.test('babaa')) count++;
// 如果正则模式是一个空字符串，则匹配所有字符串。
new RegExp('').test('abc'); // true

// exec()
// 返回匹配结果
// 如果发现匹配，就返回一个数组，成员是每一个匹配成功的子字符串，
// 否则返回null。
var s = '_x_x';
var r1 = /x/;
var r2 = /y/;
r1.exec(s) // ["x"]
r2.exec(s) // null
// 如果正则表示式包含圆括号（即含有“组匹配”），
// 则返回的数组会包括多个成员。
var s = '_x_x';
var r = /_(x)/;
r.exec(s) // ["_x", "x"]
// exec 两个属性：
// input：整个原字符串。
// index：整个模式匹配成功的开始位置（从0开始计数）。
```

## 匹配规则

**字面量字符和元字符**

字面量字符：某个字符只表示它字面的含义，例如 a，b；
元字符：有特殊含义的字符；

```js
// 点字符（.)
// 点字符（.）匹配除
//    回车（\r）、
//    换行(\n) 、
//    行分隔符（\u2028）、
//    段分隔符（\u2029）以外的所有的一个字符。
/c.t/ // cat / c2t

// 位置字符
// 用来提示字符所处的位置
// ^表示字符串的开始位置
// $表示字符串的结束位置
// test必须出现在开始位置
/^test/.test('test123') // true
/test$/.test('new test') // true

// 选择符（|）
// 表示“或关系”（OR），即cat|dog表示匹配cat或dog。
/11|22/.test('911') // true

// [Todos]
// \\、\*、+、?、()、[]、{}
```

**转义符**

反斜杠：`\+`。

```js
// 共12个字符需要转义
// ^、.、[、$、(、)、|、*、+、?、{和\\
/1\+1/.test('1+1')

// 如果使用RegExp方法生成正则对象，
// 转义需要使用两个斜杠;
// 因为字符串内部会先转义一次，
// 在字符串内部，反斜杠也是转义字符.
(new RegExp('1\\+1')).test('1+1') // true
```

**特殊字符**

对一些不能打印的特殊字符，提供了表达方法。

> \cX 表示Ctrl-[X]，其中的X是A-Z之中任一个英文字母，用来匹配控制字符。
> [\b] 匹配退格键(U+0008)，不要与\b混淆。
> \n 匹配换行键。
> \r 匹配回车键。
> \t 匹配制表符tab（U+0009）。
> \v 匹配垂直制表符（U+000B）。
> \f 匹配换页符（U+000C）。
> \0 匹配null字符（U+0000）。
> \xhh 匹配一个以两位十六进制数（\x00-\xFF）表示的字符。
> \uhhhh 匹配一个以四位十六进制数（\u0000-\uFFFF）表示的unicode字符。

**字符类**

表示有一系列字符可供选择，只要匹配其中一个就可以了。
比如：[xyz] 表示x、y、z之中任选一个匹配。

```js
// 脱字符（^）
// [^xyz]表示除了x、y、z之外都可以匹配。
/[^abc]/.test('hello world') // true
// 如果方括号内没有其他字符，即只有[^]，
// 就表示匹配一切字符，其中包括换行符，
// 而点号（.）是不包括换行符的。
var s = 'Please yes\nmake my day!';
s.match(/yes.*day/) // null
s.match(/yes[^]*day/) // [ 'yes\nmake my day']
// 注意，脱字符只有在字符类的第一个位置才有特殊含义，
// 否则就是字面含义。

// 连字符（-）
// 某些情况下，对于连续序列的字符，
// 连字符（-）用来提供简写形式，表示字符的连续范围。
// [0123456789]可以写成[0-9]，
// [A-Z]表示26个大写字母
[a-zA-Z0-9-]
[1-31] // 只代表1到3
// 连字符还可以用来指定Unicode字符的范围。
var str = "\u0130\u0131\u0132";
/[\u0128-\uFFFF]/.test(str); // true
```

**预定义模式**

某些常见模式的简写方式。

> \d 匹配0-9之间的任一数字，相当于[0-9]。
> \D 匹配所有0-9以外的字符，相当于[^0-9]。
> \w 匹配任意的字母、数字和下划线，相当于[A-Za-z0-9_]。
> \W 除所有字母、数字和下划线以外的字符，相当于[^A-Za-z0-9_]。
> \s 匹配空格（包括制表符、空格符、断行符等），相等于[\t\r\n\v\f]。
> \S 匹配非空格的字符，相当于[^\t\r\n\v\f]。
> \b 匹配词的边界。
> \B 匹配非词边界，即在词的内部。

```js
// \s的例子
/\s\w*/.exec('hello world') // [" world"]
// \b的例子
/\bworld/.test('hello world') // true
/\bworld/.test('hello-world') // true
/\bworld/.test('helloworld') // false

// 通常，正则表达式遇到换行符（\n）就会停止匹配。
var html = "<b>Hello</b>\n<i>world!</i>";
/.*/.exec(html)[0]; // "<b>Hello</b>"
// 使用\s字符类，就能包括换行符。
/[\S\s]*/.exec(html)[0]; // "<b>Hello</b>\n<i>world!</i>"
// 另一种写法（用到了非捕获组）
/(?:.|\s)*/.exec(html)[0]; // "<b>Hello</b>\n<i>world!</i>"
```

**重复类**

模式的精确匹配次数，使用大括号（{}）表示。
`{n}`表示恰好重复n次，
`{n,}`表示至少重复n次，
`{n,m}`表示重复不少于n次，不多于m次。

**量词符**

用来设定某个模式出现的次数。

`?` 问号表示某个模式出现0次或1次，等同于`{0, 1}`。
`*` 星号表示某个模式出现0次或多次，等同于`{0,}`。
`+` 加号表示某个模式出现1次或多次，等同于`{1,}`。

**贪婪模式**

三个量词符，默认情况下都是最大可能匹配，
即匹配直到下一个字符不满足匹配规则为止。
这被称为贪婪模式。

> *?：表示某个模式出现0次或多次，匹配时采用非贪婪模式。
> +?：表示某个模式出现1次或多次，匹配时采用非贪婪模式。

```js
var s = 'aaa';
s.match(/a+/) // ["aaa"]

// 如果想将贪婪模式改为非贪婪模式，
// 可以在量词符后面加一个问号。
s.match(/a+?/) // ["a"]
```

**修饰符**

表示模式的附加规则，放在正则模式的最尾部。

```js
// g修饰符
// 正则模式含有g修饰符，
// 每次都是从上一次匹配成功处，开始向后匹配。
var regex = /b/g;
var str = 'abba';
regex.test(str); // true
regex.test(str); // true
regex.test(str); // false

// i修饰符
// 默认情况下，正则对象区分字母的大小写，
// 加上i修饰符以后表示忽略大小写（ignorecase）
/abc/.test('ABC') // false
/abc/i.test('ABC') // true

// m修饰符
// 表示多行模式（multiline），会修改^和$的行为
// 加上m修饰符以后，^和$还会匹配行首和行尾，
// 即^和$会识别换行符（\n）。
/world$/.test('hello world\n') // false
/world$/m.test('hello world\n') // true
```

**组匹配**

正则表达式的括号表示分组匹配，
括号中的模式可以用来匹配分组的内容。

```js
/fred+/.test('fredd') // true
/(fred)+/.test('fredfred') // true

// 分组捕获
'abcabc'.match(/(.)b(.)/); // ['abc', 'a', 'c']
// 注意，使用组匹配时，不宜同时使用g修饰符，
// 否则match方法不会捕获分组的内容。
'abcabc'.match(/(.)b(.)/g); // ['abc', 'abc']
// 在正则表达式内部，可以用\n引用括号匹配的内容，
// n是从1开始的自然数，表示对应顺序的括号。
/(.)b(.)\1b\2/.test("abcabc"); // true
// 括号还可以嵌套。
// \1指向外层括号，\2指向内层括号。
/y((..)\2)\1/.test('yabababab'); // true
// 匹配网页标签
var tagName = /<([^>]+)>[^<]*<\/\1>/;
tagName.exec("<b>bold</b>")[1]; // 'b'
```

非捕获组

`(?:x)`称为非捕获组（Non-capturing group），
表示不返回该组匹配的内容，
即匹配的结果中不计入这个括号。

假定需要匹配foo或者foofoo，
正则表达式就应该写成`/(foo){1,2}/`，
但是这样会占用一个组匹配。
这时，就可以使用非捕获组，
将正则表达式改为`/(?:foo){1,2}/`，
它的作用与前一个正则是一样的，但是不会单独输出括号内部的内容。

```js
/(?:foo){1,2}/.exec('foo'); // ["foo"]
```

先行断言

`x(?=y)`称为先行断言（Positive look-ahead），
x只有在y前面才匹配，y不会被计入返回结果。
比如，要匹配后面跟着百分号的数字，可以写成`/\d+(?=%)/`。

“先行断言”中，括号里的部分是不会返回的。

```js
/\d+(?=%)/.exec('10%'); // ["10"]
```

先行否定断言

x(?!y)称为先行否定断言（Negative look-ahead），
x只有不在y前面才匹配，y不会被计入返回结果。
比如，要匹配后面跟的不是百分号的数字，就要写成`/\d+(?!%)/`。

“先行否定断言”中，括号里的部分是不会返回的。

```js
/\d+(?!%)/.exec('10a%'); // ["10"]
```

## 参考文档

- [RegExp对象](http://javascript.ruanyifeng.com/stdlib/regexp.html)