```
title: webpack进阶-性能优化(三)
date: 2023-07-25 12:00:00
categories:
  - 前端工程化
tags:
  - webpack
abbrlink: 
cover:
```

# 优化loader性能

## 进一步限制loader的应用范围

思路是：对于某些库，不使用loader

例如：babel-loader可以转换ES6或更高版本的语法，可是有些库本身就是用ES5语法书写的，不需要转换，使用babel-loader反而会浪费构建时间

lodash就是这样的一个库

> lodash是在ES5之前出现的库，使用的是ES3语法

通过`module.rule.exclude`或`module.rule.include`，排除或仅包含需要应用loader的场景

```js
module.exports = {
    module: {
        rules: [
            {
                test: /\.js$/,
                exclude: /lodash/,
                use: "babel-loader"
            }
        ]
    }
}
```

如果暴力一点，甚至可以排除掉`node_modules`目录中的模块，或仅转换`src`目录的模块

```js
module.exports = {
    module: {
        rules: [
            {
                test: /\.js$/,
                exclude: /node_modules/,
                //或
                // include: /src/,
                use: "babel-loader"
            }
        ]
    }
}
```

> 这种做法是对loader的范围进行进一步的限制，和noParse不冲突，因为noParse配置的模块仍然会经过loader的转化

## 缓存loader的结果

我们可以基于一种假设：如果某个文件内容不变，经过相同的loader解析后，解析后的结果也不变

于是，可以将loader的解析结果保存下来，让后续的解析直接使用保存的结果

`cache-loader`可以实现这样的功能

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        use: ['cache-loader', ...loaders]
      },
    ],
  },
};
```

有趣的是，`cache-loader`放到最前面，却能够决定后续的loader是否运行

实际上，loader的运行过程中，还包含一个过程，即`pitch`

pitch的大概原理如下：书写一个loader时，除了写loader方法外，还可以添加一个pitch函数，该函数参数接收一个文件路径，返回该路径文件的源代码。

所以第一次编译构建时，经过cache-loader的pitch时，内容不会被缓存，还是从后往前经过loader后，到达了cache-loader，此时cache-loader缓存之前loader转换的结果。

当第二次编译构建时，cache-loader发现文件有缓存过，由pitch函数直接返回该文件上次转换缓存的结果，后续在经过loader时，如果文件没有改动，就不在对该文件进行转换了。

```javascript
function loader(source){
  return `new source`
}

loader.pitch = function(filePath){
  // 可返回可不返回
  // 如果返回，返回源代码
}

module.exports = loader;

```

![image-20240917234508467](./assets/webpack进阶-性能优化(三)/image-20240917234508467.png)

`cache-loader`还可以实现各自自定义的配置，具体方式见文档

## 为loader的运行开启多线程

`thread-loader`会开启一个线程池，线程池中包含适量的线程

它会把后续的loader放到线程池的线程中运行，以提高构建效率

由于后续的loader会放到新的线程中，所以，后续的loader不能：

- 使用 webpack api 生成文件
- 无法使用自定义的 plugin api
- 无法访问 webpack options

> 在实际的开发中，可以进行测试，来决定`thread-loader`放到什么位置

**特别注意**，开启和管理线程需要消耗时间，在小型项目中使用`thread-loader`反而会增加构建时间
