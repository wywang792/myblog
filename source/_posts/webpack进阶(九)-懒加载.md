---
title: webpack进阶(九)-懒加载
categories:
  - 前端工程化
tags:
  - webpack
abbrlink: 2ee07ec0
date: 2023-08-10 12:00:00
cover:
---

# 懒加载

懒加载就是延迟加载内容，也叫动态加载，如下方式：

```javascript
// util.js

export { chunk } from "lodash-es";

// index.js

const btn = document.querySelector("button");
btn.onclick = async function() {
  // 动态加载
  // import 是ES6的草案
  // 浏览器会使用JSOP的方式远程去读取一个js模块
  // import()会返回一个promise   （* as obj）
  // const { chunk } = await import(/* webpackChunkName:"lodash" */"lodash-es");
  const { chunk } = await import("./util");
  const result = chunk([3, 5, 6, 7, 87], 2);
  console.log(result);
};


```

