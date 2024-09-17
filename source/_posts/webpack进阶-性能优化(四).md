---
title: webpack进阶-性能优化(四)
categories:
  - 前端工程化
tags:
  - webpack
abbrlink: f8465657
date: 2023-07-26 12:00:00
cover:
---

# 热替换 HMR

> 热替换并不能降低构建时间（可能还会稍微增加），但可以降低代码改动到效果呈现的时间

当使用`webpack-dev-server`时，考虑代码改动到效果呈现的过程

![image-20240918000430306](<./assets/webpack进阶-性能优化(四)/image-20240918000430306.png>)

而使用了热替换后，流程发生了变化

![image-20240918000438360](<./assets/webpack进阶-性能优化(四)/image-20240918000438360.png>)

# 使用和原理

1. 更改配置

```js
module.exports = {
  devServer: {
    hot: true, // 开启HMR
  },
  plugins: [
    // 可选，新版webpack中不配置插件，只要开启hot即可开启热替换
    new webpack.HotModuleReplacementPlugin(),
  ],
};
```

2. 更改代码

```js
// index.js

if (module.hot) {
  // 是否开启了热更新
  module.hot.accept(); // 接受热更新
}
```

首先，这段代码会参与最终运行！

当开启了热更新后，`webpack-dev-server`会向打包结果中注入`module.hot`属性

默认情况下，`webpack-dev-server`不管是否开启了热更新，当重新打包后，都会调用`location.reload`刷新页面

但如果运行了`module.hot.accept()`，将改变这一行为

`module.hot.accept()`的作用是让`webpack-dev-server`通过`socket`管道，把服务器更新的内容发送到浏览器

![image-20240918000443820](<./assets/webpack进阶-性能优化(四)/image-20240918000443820.png>)

然后，将结果交给插件`HotModuleReplacementPlugin`注入的代码执行

插件`HotModuleReplacementPlugin`会根据覆盖原始代码，然后让代码重新执行

**所以，热替换发生在代码运行期**

# 样式热替换

对于样式也是可以使用热替换的，但需要使用`style-loader`

因为热替换发生时，`HotModuleReplacementPlugin`只会简单的重新运行模块代码

因此`style-loader`的代码一运行，就会重新设置`style`元素中的样式

而`mini-css-extract-plugin`，由于它生成文件是在**构建期间**，运行期间无法改动文件，因此它对于热替换是无效的
