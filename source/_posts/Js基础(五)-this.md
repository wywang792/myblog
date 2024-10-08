---
title: Js基础(二)-数据和数据类型
categories:
  - 前端开发
tags:
  - javascript
abbrlink: 59df9dcb
date: 2023-05-26 00:00:00
cover:
---

# 这是啥

<img src="./assets/Js基础(五)-this/20211213054951.png" alt="image-20211213054946368"  />

不同的场景，**这** 指代的含义不同，JS中的**this**关键字也是如此：

在全局代码中使用this，指代全局对象

> 在真实的开发中，很少在全局代码使用this

**函数中使用this，它的指向完全取决于函数是如何被调用的**


  | 调用方式         | 示例                | 函数中的this指向  |
  | ---------------- | ------------------- | ----------------- |
  | **通过new调用**  | `new method()`      | 新对象            |
  | **直接调用**     | `method()`          | 全局对象          |
  | **通过对象调用** | `obj.method()`      | 前面的对象        |
  | **call**         | `method.call(ctx)`  | call的第一个参数  |
  | **apply**        | `method.apply(ctx)` | apply的第一个参数 |
  |                  |                     |                   |

  


 