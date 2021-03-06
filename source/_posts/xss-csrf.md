---
title: XSS 和 CSRF 基本概念
date: 2018-01-11 20:10:10
tags:
  - safe
---

### XSS

XSS，跨站脚本攻击，即 Cross Site Scripting，
为了和CSS不重名，所以取名为XSS。

#### 原理

XSS攻击者通过畸形的输入，
将恶意的js代码插入到了页面中。
当其他用户浏览该网页时，恶意的js代码会被执行，从而达到攻击的目的。

XSS攻击发生的根本原因是**对用户输入的信任**。

#### 分类

- 反射型XSS；
- 存储型XSS；
- DOM-XSS；

#### 反射型XSS

反射型XSS，即将用户的输入直接传到后台（不保存)，后台并未经过任何过滤，直接将输入数据返回给前台。

比如一个页面，其内容就是根据url中查询参数param的值来生成的。那么我们可以请求如下的url：

```
http://example.com/scene/?param=<script>alert('这是一个XSS攻击')</script>
```

这样当我们将这个url放出去供其他用户访问时，就会出现alert。因为此时页面内容如下：

```
<div><script>alert('这是一个XSS攻击')</script></div>
```

#### 存储型XSS

存储型XSS的攻击过程大致如下：

M用户的畸形输入
-> 后台服务器未经任何处理就存储
-> 其他用户T请求数据
-> M用户的畸形输入返回给T
-> 用户T打开页面，XSS代码运行

#### DOM-XSS

纯粹发生在客户端的XSS攻击。

#### 如何防范XSS?

因此我们要对用户的输入（url参数/post数据等）当做不可信数据来处理。

那能否从根本上这种行为呢？

Content Security Policy

### CSRF

CSRF，跨站请求伪造，即`Cross Site Request Forgery`。

#### 原理

CSRF可以在受害者毫不知情的情况下以受害者名义发送请求。
其原理是**借助受害者的cookie来骗取服务器的信任**。

比如，受害者T登录了某个银行网站，
sessionId等信息就会保存在浏览器中。
这时候，用户T又打开了CSRF攻击网站，
这个网站通过表单自动提交来向银行网站发起一次转账请求，
这样，受害者T的银行账户的钱可能就被转走了。

这个过程成功的关键点在于：
**用户登录了银行网站，并且session还未过期的时候，打开了CSRF攻击网站**。

#### 如何防范CSRF?

- 检查HTTP Referer字段
- 添加校验TOKEN
