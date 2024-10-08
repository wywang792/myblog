---
title: Js兼容性(三)-babel插件
categories:
  - 前端工程化
tags:
  - javascript
abbrlink: 93fec053
date: 2023-08-26 12:00:00
cover:
---

# babel 插件

除了预设可以转换代码之外，插件也可以转换代码，它们的顺序是：

- 插件在 Presets 前运行。
- 插件顺序从前往后排列。
- Preset 顺序是颠倒的（从后往前）。

通常情况下，`@babel/preset-env`只转换那些已经形成正式标准的语法，对于某些处于早期阶段、还没有确定的语法不做转换。

如果要转换这些语法，就要单独使用插件

下面随便列举一些插件

## `@babel/plugin-proposal-class-properties`

该插件可以让你在类中书写初始化字段

```js
class A {
  a = 1;
  constructor() {
    this.b = 3;
  }
}
```

## `@babel/plugin-proposal-function-bind`

该插件可以让你轻松的为某个方法绑定 this

```js
function Print() {
  console.log(this.loginId);
}

const obj = {
  loginId: "abc",
};

obj::Print(); //相当于：Print.call(obj);
```

> 遗憾的是，目前 vscode 无法识别该语法，会在代码中报错，虽然并不会有什么实际性的危害，但是影响观感

## `@babel/plugin-proposal-optional-chaining`

```js
const obj = {
  foo: {
    bar: {
      baz: 42,
    },
  },
};

const baz = obj?.foo?.bar?.baz; // 42

const safe = obj?.qux?.baz; // undefined
```

## `babel-plugin-transform-remove-console`

该插件会移除源码中的控制台输出语句

```js
console.log("foo");
console.error("bar");
```

编译后

```js

```

## `@babel/plugin-transform-runtime`

用于提供一些公共的 API，这些 API 会帮助代码转换
