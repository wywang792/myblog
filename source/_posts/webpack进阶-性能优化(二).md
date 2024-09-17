```
title: webpack进阶-性能优化(五)
date: 2023-07-22 12:00:00
categories:
  - 前端工程化
tags:
  - webpack
abbrlink: 
cover:
```

# 减少模块解析

## 什么叫做模块解析？

模块解析包括：抽象语法树分析、依赖分析、模块语法替换

![image-20240917233448984](./assets/webpack进阶-性能优化(二)/image-20240917233448984.png)

## 不做模块解析会怎样？

如果某个模块不做解析，该模块经过loader处理后的代码就是最终代码。

如果没有loader对该模块进行处理，该模块的源码就是最终打包结果的代码。

如果不对某个模块进行解析，可以缩短构建时间

![image-20240917233452706](./assets/webpack进阶-性能优化(二)/image-20240917233452706.png)

## 哪些模块不需要解析？

模块中无其他依赖：一些已经打包好的第三方库，比如jquery

## 如何让某个模块不要解析？

配置`module.noParse`，它是一个正则，被正则匹配到的模块不会解析

```javascript
module.exports = {
    mode: "development",
    devtool: "source-map",
    module: {
        noParse: /jquery|lodash/
    }
}

```

