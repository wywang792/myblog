---
title: webpack进阶-常用扩展
categories:
  - 前端工程化
tags:
  - webpack
abbrlink: b10b8d2a
date: 2023-07-09 12:00:00
cover:
---

## 开发服务器：webpack-dev-server

在**开发阶段**，目前遇到的问题是打包、运行、调试过程过于繁琐，回顾一下我们的操作流程：

1. 编写代码
2. 控制台运行命令完成打包
3. 打开页面查看效果
4. 继续编写代码，回到步骤 2

并且，我们往往希望把最终生成的代码和页面部署到服务器上，来模拟真实环境

为了解决这些问题，webpack 官方制作了一个单独的库：**webpack-dev-server**

它**既不是 plugin 也不是 loader**

先来看看它怎么用

1. 安装
2. 执行`webpack-dev-server`命令

`webpack-dev-server`命令几乎支持所有的 webpack 命令参数，如`--config`、`-env`等等，你可以把它当作 webpack 命令使用

这个命令是专门为开发阶段服务的，真正部署的时候还是得使用 webpack 命令

当我们执行`webpack-dev-server`命令后，它做了以下操作：

1. 内部执行 webpack 命令，传递命令参数
2. 开启 watch
3. 注册 hooks：类似于 plugin，webpack-dev-server 会向 webpack 中注册一些钩子函数，主要功能如下：
   1. 将资源列表（aseets）保存起来
   2. 禁止 webpack 输出文件
4. 用 express 开启一个服务器，监听某个端口，当请求到达后，根据请求的路径，给予相应的资源内容

**配置**

针对 webpack-dev-server 的配置，参考：https://www.webpackjs.com/configuration/dev-server/

常见配置有：

- port：配置监听端口
- proxy：配置代理，常用于跨域访问
- stats：配置控制台输出内容

配置如下：

```javascript
// 需要使用npm install -D webpack-dev-server 安装
// 之后可以使用 npx webpack-dev-server 启动开发服务器

module.exports = {
    mode: "development",
    devtool: "source-map",
    output: {
        filename: "scripts/[name].[chunkhash:5].js"
    },
    devServer: {
        port: 8000, // 启动开发服务器的端口
        open: true, // 是否自动打开页面
        proxy: { // 代理规则
            // 要被代理的接口，”/api“表示接口路径中包含api的所有接口被代理
            "/api": {
                // 目标服务器地址
                target: "http://open.duyiedu.com",
                // 更改请求头中的host和origin，有的接口的后端服务会对该字段进行校验
                changeOrigin: true,
                // 路径重写，{ '/api': '' }是指将路径中/api替换为空字符串
                pathRewrite: { '/api': '' },
                // 如果目标服务器是HTTPS，需要配置此项
              	secure: false,
                // 如果需要代理 WebSocket 请求，则需要将该项设置为 true
                ws: false
            }
        }，
       stats: {
          	modules: false,
          	colors: true
       }
    }
}

// 配置代理的原因: 假设我们在项目中通过ajax或者axios请求了其他域名的接口，因为我们自己网站的域名是
// http://localhost:port，在浏览器中请求其他域名的接口时就会被浏览器的跨越策略拦截，不被允许访问
// 如果我们配置了代理的话，相当于我们的接口请求是由开发服务器发出的，因为开发服务器并没有跨域策略，这样就不会出现跨域的问题
// 跨域策略是浏览器为了防止网站肆意访问其他网站资源所产生的一种预防策略，如果是服务器的话并不会出现跨域

// 注：有时候在配置key值和pathRewrite的路径时，我们会在路径前加一个 ^，比如
'^/api': {
    target: 'http://localhost:3000',
    pathRewrite: { '^/api': '' }
}

// 这是一个正则表达式的标志，用于精确匹配路径的开头部分。
// ^/api就是表示匹配以/api开头的路径，而不是匹配路径中包含/api的全部路径
```

## 清除输出目录：clean-webpack-plugin

在我们的 webpack 编译生成文件时，如果配置了 output.filename 中包含 hash 值的内容，比如：

```javascript
module.exports = {
  mode: "development",
  devtool: "source-map",
  output: {
    filename: "[name].[chunkhash:5].js",
  },
};
```

这时，webpack 每次打包都会产生新的文件添加到 dist 目录下，dist 下的文件就会越来越多，而且我们无法知道那个文件是我们最后一次打包出来的

此时，如果配置了 clean-webpack-plugin，它就会在每次打包产生文件之前先清除构建目录下的文件，配置如下

```javascript
var { CleanWebpackPlugin } = require("clean-webpack-plugin");

module.exports = {
  mode: "development",
  devtool: "source-map",
  output: {
    filename: "[name].[chunkhash:5].js",
  },
  plugins: [new CleanWebpackPlugin()],
};
```

## 自动生成页面：html-webpack-plugin

webpack 自身打包出来的文件只有 js 文件，不过不使用插件的话，我们就需要自己创建 html 文件，将打包出来的 js 文件通过 script 标签引入到页面上，这对我们开发来说，也是增加了一部分没有必要的工作量

此时，如果配置了 html-webpack-plugin，那么我们可以在 public 目录下添加一个 html 文件，这样在每次打包生成文件时，这个插件首先会帮我们将 public 目录下的文件作为模板文件复制到构建目录中去，并且自动在文件中追加 script 标签，引用打包出的 chunk 文件。配置如下：

```javascript
// 如果有多个chunk文件需要打包，引用到不同的页面上，那我们可以配置多个HtmlWebpackPlugin，如下
// template: 配置模板文件
// filename: 模板文件复制到构建目录下的文件名
// chunks: 这个模板文件中要引用的chunk的key值，注意是在entry中配置时的key值，而不是chunk的文件名

const { CleanWebpackPlugin } = require("clean-webpack-plugin");
const HtmlWebpackPlugin = require("html-webpack-plugin");
module.exports = {
  mode: "development",
  devtool: "source-map",
  entry: {
    home: "./src/index.js",
    a: "./src/a.js",
  },
  output: {
    filename: "scripts/[name].[chunkhash:5].js",
  },
  plugins: [
    new CleanWebpackPlugin(),
    new HtmlWebpackPlugin({
      template: "./public/index.html",
      filename: "home.html",
      chunks: ["home"],
    }),
    new HtmlWebpackPlugin({
      template: "./public/index.html",
      filename: "a.html",
      chunks: ["a"],
    }),
  ],
};
```

## 复制静态资源：copy-webpack-plugin

html-webpack-plugin 只会将配置的 html 页面复制到构建目录下，如果我们项目中用到了图片、视频等静态资源文件的话，还是需要我们手动复制过去，这样也很不方便

copy-webpack-plugin 就是帮助我们复制静态资源文件的，配置如下：

```javascript
// from: 要复制的静态资源的目录，相对于webpack.config.js文件的相对路径
// to: 要复制到的目录，这是相对于构建目录的相对路径，如果没有修改webpack最终的路径，那么就是相对dist目录的路径

const { CleanWebpackPlugin } = require("clean-webpack-plugin");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const CopyPlugin = require("copy-webpack-plugin");
module.exports = {
  mode: "development",
  devtool: "source-map",
  output: {
    filename: "scripts/[name].[chunkhash:5].js",
  },
  plugins: [
    new CleanWebpackPlugin(),
    new HtmlWebpackPlugin({
      template: "./public/index.html",
    }),
    new CopyPlugin([{ from: "./public", to: "./" }]),
  ],
};
```

**注：**这里的 copy-webpack-plugin 要与下面要介绍 file-loader 进行区分，copy-webpack-plugin 只是**单纯将静态资源复制到构建目录中去，并不会做其他的处理**，这样复制过来的资源，适用于我们直接在页面上写死的资源，比如：

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>Document</title>
  </head>
  <body>
    <h1>webpack</h1>
    <p>
      <img src="./img/webpack.png" alt="" />
    </p>
  </body>
</html>
```

这里引用的图片就是我们写死在 index.html 上的，我们使用 copy-webpack-plugin 将图片复制到构建目录可以直接使用。

下面要介绍的 file-loader 则适用于在 index.js 中使用 require 或 import 的方式导入的文件。

## 普通文件处理：file-loader 与 url-loader

### file-loader

比如我们在 index.js 中引用了一张图片，要在某个合适的时机添加到页面上，代码如下：

```javascript
// 希望导入的模块结果是一个可用的资源路径

// 这里如果使用commonjs导入图片时，要在后面加上default
// 因为file-loader导出时，默认是使用es6模块化方式导出的
// webpack对es6和commonjs做了兼容，用es6导出，如果要用commonjs导入的话，需要加上.default
// 也可以直接用es6模块进行导入

// import png from "./assets/webpack.png"

const png = require("./assets/webpack.png").default;

if (Math.random() < 0.5) {
  var img = document.createElement("img");
  img.src = png;
  document.body.appendChild(img);
}
```

这个时候，如果我们直接进行构建的话，则会出现报错，因为 webpack 只能处理 js 文件，这里发现一张图片时，webpack 无法将这个图片文件的内容抽象成 AST 语法树，那这里就会报错了。

此时，我们就需要用到 file-loader 了，file-loader 的作用，首先它会将这个依赖的文件根据输出到构建目录中去，然后会将文件内容导出为一个路径，类似我们之前开发的自定义插件 img-loader。

```javascript
// file-loader
function loader(source) {
  // source：文件内容（图片内容 buffer）
  // 1. 生成一个具有相同文件内容的文件到输出目录
  // 2. 返回一个路径 export default "文件名"
}
```

这样，我们在 js 中引入一些普通文件后，编译时才不会报错，配置如下：

```javascript
const { CleanWebpackPlugin } = require("clean-webpack-plugin")
const HtmlWebpackPlugin = require('html-webpack-plugin')
const CopyPlugin = require('copy-webpack-plugin');
module.exports = {
    mode: "development",
    devtool: "source-map",
    output: {
        filename: "scripts/[name].[chunkhash:5].js"
    },
    module: {
    		rules: [
    				test: /\.(png)|(jpg)|(gif)$/,
    				use: [{
    						loader: 'file-loader'
    						options: {
          					// [name]指原来文件的文件名
          					// [ext]指原来文件的后缀名
          					// [hash]指原来文件内容生成的hash值，这里要区分chunk的hash
          					// [hash]用来防止文件名重复，也可以解决文件缓存问题
          					// 这里也可以在前面加上路径
          					// file-loader不会管原来的文件在那个路径下，会将所有文件都按照这里的配置生成到同一个目录下
    								name: 'imgs/[name]-[hash:5].[ext]'
    						}
    				}]
    		]
    }
    plugins: [
        new CleanWebpackPlugin(),
        new HtmlWebpackPlugin({
            template: "./public/index.html"
        }),
        new CopyPlugin([
            { from: "./public", to: "./" }
        ])
    ]
}
```

### url-laoder

url-loader 内部也用到了 file-loader，但并不会像 file-loader 那么暴力，只要是静态文件，就全部输出成文件

url-laoder 允许我们根据需要将文件转换为 base64 格式的字符串，以减少 http 请求

```javascript
// url-loader
function loader(source) {
  // source：文件内容（图片内容 buffer）
  // 1. 根据buffer生成一个base64编码
  // 2. 返回一段代码   export default "base64编码"
}
```

配置如下：

```javascript
const { CleanWebpackPlugin } = require("clean-webpack-plugin")
const HtmlWebpackPlugin = require('html-webpack-plugin')
const CopyPlugin = require('copy-webpack-plugin');
module.exports = {
    mode: "development",
    devtool: "source-map",
    output: {
        filename: "scripts/[name].[chunkhash:5].js"
    },
    module: {
    		rules: [
    				test: /\.(png)|(jpg)|(gif)$/,
    				use: [{
    						loader: 'url-loader'
    						options: {
          					// limit: false, // 不限制任何大小，所有经过loader的文件进行base，64编码返回
          					limit: 10240, // 限制10K以下的文件，进行base64编码，否则交给file-loader进行处理
    								name: 'imgs/[name]-[hash:5].[ext]'
    						}
    				}]
    		]
    }
    plugins: [
        new CleanWebpackPlugin(),
        new HtmlWebpackPlugin({
            template: "./public/index.html"
        }),
        new CopyPlugin([
            { from: "./public", to: "./" }
        ])
    ]
}
```

### 解决路径问题

在使用 file-loader 或 url-loader 时，可能会遇到一个非常有趣的问题

比如，通过 webpack 打包的目录结构如下：

```yaml
dist
    |—— img
        |—— a.png  #file-loader生成的文件
    |—— scripts
        |—— main.js  #export default "img/a.png"
    |—— html
        |—— index.html #<script src="../scripts/main.js" ></script>
```

这种问题发生的根本原因：模块中的路径来自于某个 loader 或 plugin，当产生路径时，loader 或 plugin 只有相对于 dist 目录的路径，并不知道该路径将在哪个资源中使用，从而无法确定最终正确的路径

面对这种情况，需要依靠 webpack 的配置 publicPath 解决

比如有如下配置：

```javascript
const HtmlWebpackPlugin = require("html-webpack-plugin");

module.exports = {
  mode: "development",
  devtool: "source-map",
  output: {
    filename: "scripts/[name].[chunkhash:5].js",
  },
  module: {
    rules: [
      {
        test: /\.(png)|(gif)|(jpg)$/,
        use: [
          {
            loader: "file-loader",
            options: {
              name: "imgs/[name].[hash:5].[ext]",
            },
          },
        ],
      },
    ],
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: "./public/index.html",
      filename: "html/index.html",
    }),
  ],
  devServer: {
    open: true,
    openPage: "html/index.html",
  },
};
```

此时没有配置 publicPath，我们最终打包生成的目录如下：

```yaml
dist
|—— img
|—— a.png
|—— scripts
|—— main.js
|—— html
|—— index.html
```

```html
<!-- index.html -->
<!-- index.html中正确的引用了index.js的路径 -->

<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>Document</title>
  </head>
  <body>
    <h1>webpack</h1>
    <script type="text/javascript" src="../scripts/main.43a64.js"></script>
  </body>
</html>
```

这时候，如果访问 html/index.html 可以正确访问到 index.js

```javascript
// index.js

import png from "./assets/webpack.png";
console.log(png);
var img = document.createElement("img");
img.src = png;
document.body.appendChild(img);
```

index.js 引入的 img 是一个相对路径的地址，当网络请求 img 图片时，会出现这种情况：

![image-20240914155044302](./assets/webpack进阶-常用扩展/image-20240914155044302.png)

我们访问 html 下的 index.html 时，main.js 中引用的图片时相对与 html 路径的，此时就出现了无法访问到图片的情况。

下面我说说 publicPath 的实际用途，publicPath 顾名思义，就是一个公共路径，就是一个字符串，它在 webpack 本身中并没有什么实际的用途，但是在某些 loader 或者 plugin 中，在拼接路径时，如果配置了 publicPath，那么就会在路径前加上 publicPath。

- 没有配置 publicPath，HtmlWebpackPlugin 按照../script/main.js 的相对路径来引用 js 文件，正确的找到最终打包的 main.js 的位置
- 配置了 publicPath 为空字符串“”，此时 HtmlWebpackPlugin 就会在配置的路径前面拼接上我们配置的空字符串，按照 script/main.js 的相对路径来引用 js 文件，无法找到 js 文件
- 配置了 publicPath 为"/"，此时 HtmlWebpackPlugin 就会按照/script/main.js 的绝对路径来引用 js 文件，能够找到 js 文件，而且 js 中引用的图片，也是按照 /imgs/webpack.jpg 绝对路径来请求文件，也能够请求到图片

**一般情况下，我们配置 output.publicPath 为“/”即可**

```javascript
output: {
  filename: "scripts/[name].[chunkhash:5].js";
  publicPath: "/";
}
```

## webpack 内置插件

所有的 webpack 内置插件都作为 webpack 的静态属性存在的，使用下面的方式即可创建一个插件对象

```javascript
const webpack = require("webpack");

new webpack.插件名(options);
```

### DefinePlugin

全局常量定义插件，使用该插件通常定义一些常量值，例如：

```javascript
new webpack.DefinePlugin({
  PI: `Math.PI`, // PI = Math.PI
  VERSION: `"1.0.0"`, // VERSION = "1.0.0"
  DOMAIN: JSON.stringify("duyi.com"),
});
```

这样一来，在源码中，我们可以直接使用插件中提供的常量，当 webpack 编译完成后，会自动替换为常量的值

### BannerPlugin

它可以为每个 chunk 生成的文件头部添加一行注释，一般用于添加作者、公司、版权等信息

```javascript
new webpack.BannerPlugin({
  banner: `
  hash:[hash]
  chunkhash:[chunkhash]
  name:[name]
  author:yuanjin
  corporation:duyi
  `,
});
```

### ProvidePlugin

自动加载模块，而不必到处 import 或 require

```javascript
new webpack.ProvidePlugin({
  $: "jquery",
  _: "lodash",
});
```

我们在源码中可以直接使用，而不需要再次导入

```javascript
$("#item"); // <= 起作用
_.drop([1, 2, 3], 2); // <= 起作用
```
