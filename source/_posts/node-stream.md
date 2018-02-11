---
title: Stream
date: 2018-02-09 13:42:10
categories: NodeJS
tags:
  - js
---

## 为什么需要 Stream

在处理信息时，
不需要把信息全部加载到内存后，再处理，
而是，可以，
读取一部分，处理一部分，
直至结束。

## Readable Stream

```js
var Readable = require('stream').Readable;

var rs = new Readable;
rs.push('servers ');
rs.push('are listening on\n');
rs.push('3000 and 4000\n');
rs.push(null);

rs.pipe(process.stdout);
```

