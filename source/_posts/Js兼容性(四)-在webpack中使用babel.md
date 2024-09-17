---
title: Js兼容性(四)-在webpack中使用babel
categories:
  - 前端工程化
tags:
  - javascript
abbrlink: d9a194fb
date: 2023-09-08 12:00:00
cover:
---

# 在webpack中使用babel

## 安装

```
npm i -D @babel/core @babel/preset-env babel-loader core-js regenerator-runtime
```

## 配置

```
// .babelrc

{
    "presets": [
        ["@babel/preset-env", {
            "useBuiltIns": "usage",
            "corejs": 3
        }]
    ]
}
```

```
// .browserslistrc

last 3 version
> 1%
not ie <= 8
```

```javascript
// webpack.config.js

module.exports = {
    mode: "development",
    devtool: "source-map",
    module: {
        rules: [
            { test: /\.js$/, use: "babel-loader" }
        ]
    }
}

```

