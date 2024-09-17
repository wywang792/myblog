```
title: CSS工程化(六)-预编译器less
date: 2023-09-25 12:00:00
categories:
  - 前端工程化
tags:
  - css
abbrlink: 
cover:
```

# CSS预编译器

## 基本原理

编写css时，受限于css语言本身，常常难以处理一些问题：

- 重复的样式值：例如常用颜色、常用尺寸
- 重复的代码段：例如绝对定位居中、清除浮动
- 重复的嵌套书写

由于官方迟迟不对css语言本身做出改进，一些第三方机构开始想办法来解决这些问题

其中一种方案，便是预编译器

预编译器的原理很简单，即使用一种更加优雅的方式来书写样式代码，通过一个编译器，将其转换为可被浏览器识别的传统css代码

![image-20240918011931776](./assets/CSS工程化(六)-预编译器less/image-20240918011931776.png)

目前，最流行的预编译器有**LESS**和**SASS**，由于它们两者特别相似，因此仅学习一种即可![image-20240918011936010](./assets/CSS工程化(六)-预编译器less/image-20240918011936010.png)

> less官网：http://lesscss.org/
>
> less中文文档1（非官方）：http://lesscss.cn/
>
> less中文文档2（非官方）：https://less.bootcss.com/
>
> sass官网：https://sass-lang.com/
>
> sass中文文档1（非官方）：https://www.sass.hk/
>
> sass中文文档2（非官方）：https://sass.bootcss.com/

## LESS的安装和使用

从原理可知，要使用LESS，必须要安装LESS编译器

LESS编译器是基于node开发的，可以通过npm下载安装

```shell
npm i -D less
```

安装好了less之后，它提供了一个CLI工具`lessc`，通过该工具即可完成编译

```shell
lessc less代码文件 编译后的文件
```

试一试:

新建一个`index.less`文件，编写内容如下：

```less
// less代码
@red: #f40;

.redcolor {
    color: @red;
}
```

运行命令：

```shell
lessc index.less index.css
```

可以看到编译之后的代码：

```css
.redcolor {
  color: #f40;
}
```

## LESS的基本使用

具体的使用见文档：https://less.bootcss.com/

- 变量
- 混合
- 嵌套
- 运算
- 函数
- 作用域
- 注释
- 导入

# 在webpack中使用LESS

```shell
// 安装less和less-loader
npm install less less-loader
```

```javascript
// webpack.config.js
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
    mode: "development",
    devServer: {
        open: true
    },
    module: {
        rules: [
            { test: /\.css$/, use: ["style-loader", "css-loader"] },
            { test: /\.less$/, use: ["style-loader", "css-loader?modules", "less-loader"] },
        ]
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: "./public/index.html"
        })
    ]
}

```

配置好后，即可在项目中书写less文件，如：

```less
// assets/index.less

@color: #f40;

.main {
    color: @color;
    font-size: 2em;
}
```

```javascript
// index.js

import styles from "./assets/index.less"
var div = document.getElementById("app");
div.className = styles.main;

```

