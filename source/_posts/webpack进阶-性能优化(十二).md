```
title: webpack进阶-性能优化(十二).md
date: 2023-08-20 12:00:00
categories:
  - 前端工程化
tags:
  - webpack
abbrlink: 
cover:
```

# gzip

gzip是一种压缩文件的算法

主要依赖**CmpressionWebpackPlugin**插件实现，同时需要后端服务进行支持，对于nginx来说，需要配置gzip为on

# B/S结构中的压缩传输

![image-20240918005702991](./assets/webpack进阶-性能优化(十二)/image-20240918005702991.png)

优点：传输效率可能得到大幅提升

缺点：服务器的压缩需要时间，客户端的解压需要时间

# 使用webpack进行预压缩

使用`compression-webpack-plugin`插件对打包结果进行预压缩，可以移除服务器的压缩时间

![image-20240918005708588](./assets/webpack进阶-性能优化(十二)/image-20240918005708588.png)

```javascript
const { CleanWebpackPlugin } = require("clean-webpack-plugin");
const CmpressionWebpackPlugin = require("compression-webpack-plugin")
module.exports = {
  mode: "production",
  optimization: {
    splitChunks: {
      chunks: "all"
    }
  },
  plugins: [
    new CleanWebpackPlugin(),
    new CmpressionWebpackPlugin({
      // 对js文件进行gzip压缩
      test: /\.js/,
      // 对能达到该压缩比率的文件进行压缩
      minRatio: 0.5
    })
  ]
};

```

