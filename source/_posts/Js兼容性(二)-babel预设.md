---
title: Js兼容性(二)-babel预设
categories:
  - 前端工程化
tags:
  - javascript
abbrlink: 32dad9ce
date: 2023-08-28 12:00:00
cover:
---

# babel预设

babel有多种预设，最常见的预设是`@babel/preset-env`

`@babel/preset-env`可以让你使用最新的JS语法，而无需针对每种语法转换设置具体的插件

**配置**

```json
{
    "presets": [
        "@babel/preset-env"
    ]
}
```

**兼容的浏览器**

`@babel/preset-env`需要根据兼容的浏览器范围来确定如何编译，和postcss一样，可以使用文件`.browserslistrc`来描述浏览器的兼容范围

```
last 3 version
> 1%
not ie <= 8
```

**自身的配置**

和`postcss-preset-env`一样，`@babel/preset-env`自身也有一些配置

> 具体的配置见：https://www.babeljs.cn/docs/babel-preset-env#options

配置方式是：

```json
{
    "presets": [
        ["@babel/preset-env", {
            "配置项1": "配置值",
            "配置项2": "配置值",
            "配置项3": "配置值"
        }]
    ]
}
```

其中一个比较常见的配置项是`usebuiltins`，该配置的默认值是false

它有什么用呢？由于该预设仅转换新的语法，并不对新的API进行任何处理

例如：

```js
new Promise(resolve => {
    resolve()
})
```

转换的结果为

```js
new Promise(function (resolve) {
  resolve();
});
```

如果遇到没有Promise构造函数的旧版本浏览器，该代码就会报错

而配置`usebuiltins`可以在编译结果中注入这些新的API，它的值默认为`false`，表示不注入任何新的API，可以将其设置为`usage`，表示根据API的使用情况，按需导入API

此时需要安装core-js，如果需要使用更新的async/await，还需要引入regenerator-runtime

```
npm i -D core-js regenerator-runtime
```

```json
{
    "presets": [
        ["@babel/preset-env", {
            "useBuiltIns": "usage",
            "corejs": 3 // babel默认支持的时core-js 2
        }]
    ]
}
```
