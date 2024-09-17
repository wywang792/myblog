---
title: webpack进阶-loader
categories:
  - 前端工程化
tags:
  - webpack
abbrlink: f77f15dd
date: 2023-07-07 12:00:00
cover:
---



## Loader简介

webpack做的事情，仅仅是分析出各种模块的依赖关系，然后形成资源列表，最终打包生成到指定的文件中。
更多的功能需要借助webpack loaders和webpack plugins完成。

loader本质上是一个函数，它的作用是将某个源码字符串转换成另一个源码字符串返回。

![image-20240912231533718](./assets/webpack进阶-loader/image-20240912231533718.png)

loader函数的将在模块解析的过程中被调用，以得到最终的源码。

### 全流程

![image-20240912231739424](./assets/webpack进阶-loader/image-20240912231739424.png)

### chunk中解析模块的流程

![image-20240912231819059](./assets/webpack进阶-loader/image-20240912231819059.png)

### chunk中解析模块的更详细流程

![image-20240912231920904](./assets/webpack进阶-loader/image-20240912231920904.png)

### 处理loaders的流程

![image-20240912232004677](./assets/webpack进阶-loader/image-20240912232004677.png)

### loader配置：

#### 完整配置

```javascript
module.exports = {
    module: { //针对模块的配置，目前版本只有两个配置，rules、noParse
        rules: [ //模块匹配规则，可以存在多个规则
            { //每个规则是一个对象
                test: /\.js$/, //匹配的模块正则
                use: [ //匹配到后应用的规则模块
                    {  //其中一个规则
                        loader: "模块路径", //loader模块的路径，该字符串会被放置到require中
                        options: { //向对应loader传递的额外参数

                        }
                    }
                ]
            }
        ]
    }
}
```

#### 简化配置

```javascript
module.exports = {
    module: { //针对模块的配置，目前版本只有两个配置，rules、noParse
        rules: [ //模块匹配规则，可以存在多个规则
            { //每个规则是一个对象
                test: /\.js$/, //匹配的模块正则
                use: ["模块路径1", "模块路径2"]//loader模块的路径，该字符串会被放置到require中
            }
        ]
    }
}
```

## 自定义Loader(1)

我们来实现一个自定义的loader

实现功能：将写好的css样式添加到页面

```
|-- dist
	|-- index.html  // 写好的html页面，引用编译好的js文档
	|-- main.js
|-- loaders  // 存放自定义的loader
	|-- style-loader.js  // 我们要写的loader
|-- src
	|-- assets
		|-- index.css  // 简单写的一个css样式
	|-- index.js  // 导入css样式
|-- package-lock.json
|-- package.json
|-- webpack.config.js  // webpack配置文件
```

```html
// index.html

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <script src="./main.js"></script>
</body>
</html>
```

```javascript
// index.js

var content = require("./assets/index.css")

console.log(content); // css的源码字符串
```

```javascript
// index.css

body {
    background: #333;
    color: #fff;
}
```

```javascript
// style-loader.js

module.exports = function (sourceCode) {
    var code = `var style = document.createElement("style");
    style.innerHTML = \`${sourceCode}\`;
    document.head.appendChild(style);
    module.exports = \`${sourceCode}\``;
    return code;
}
```

```javascript
// webpack.config.js

module.exports = {
    mode: "development",
    devtool: "source-map",
    module: {
        rules: [{
            test: /\.css$/,
            use: ["./loaders/style-loader"]
        }]
    }
}
```

```json
// package.json

{
  "name": "test",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "webpack": "^5.94.0",
    "webpack-cli": "^5.1.4"
  }
}
```



### 代码讲解

1、在入口文件index.js中导入了写好的css文件index.css

2、webpack从入口文件开始分析代码，获取依赖

3、webpack发现导入了一个css文件（index.css），这个css文件需要loader处理，将这个文件的源代码作为参数传递给style-loader.js中的函数进行处理；

4、style-loader.js中的函数，创建了一个style标签，将我们写好的css样式添加到style标签中，并将标签追加到html页面的head中，最后，将css中的源样式进行了导出

接下来，让我们看看最终的编译结果，main.js

```javascript
// main.js

(() => {
  // webpackBootstrap
  var __webpack_modules__ = {
    "./src/assets/index.css": (module) => {
      eval(
        'var style = document.createElement("style");\n    style.innerHTML = `body{\n    background: red;\n    color: #fff;\n}\n`;\n    document.head.appendChild(style);\n    module.exports = `body{\n    background: red;\n    color: #fff;\n}\n`\n\n//# sourceURL=webpack://test/./src/assets/index.css?'
      );
    },

    "./src/index.js": (
      __unused_webpack_module,
      __unused_webpack_exports,
      __webpack_require__
    ) => {
      eval(
        'var content = __webpack_require__(/*! ./assets/index.css */ "./src/assets/index.css")\n\nconsole.log(content); //css的源码字符串\r\n\n\n//# sourceURL=webpack://test/./src/index.js?'
      );
    },
  };
  /************************************************************************/
  // The module cache
  var __webpack_module_cache__ = {};

  // The require function
  function __webpack_require__(moduleId) {
    // Check if module is in cache
    var cachedModule = __webpack_module_cache__[moduleId];
    if (cachedModule !== undefined) {
      return cachedModule.exports;
    }
    // Create a new module (and put it into the cache)
    var module = (__webpack_module_cache__[moduleId] = {
      // no module.id needed
      // no module.loaded needed
      exports: {},
    });

    // Execute the module function
    __webpack_modules__[moduleId](module, module.exports, __webpack_require__);

    // Return the exports of the module
    return module.exports;
  }

  /************************************************************************/

  // startup
  // Load entry module and return exports
  // This entry module can't be inlined because the eval devtool is used.
  var __webpack_exports__ = __webpack_require__("./src/index.js");
})();

```

### 编译结果分析

main.js从入口文件index.js开始执行，发现存在index.css模块，执行index.css模块的代码，将style元素追加到了head中，并且导出了源样式，执行结束后继续执行index.js中的代码，输出index.css中导出的源样式

### 最终效果

![image-20240913002531606](./assets/webpack进阶-loader/image-20240913002531606.png)

![image-20240913002604622](./assets/webpack进阶-loader/image-20240913002604622.png)

## 自定义Loader(2)

我们再来实现一个自定义的loader

实现功能：我们在js文件中引入图片时，如果图片大小小于指定值，则将图片转换为base64格式，如果图片大于指定值，则输出图片路径

```
|-- dist
	|-- index.html  // 写好的html页面，引用编译好的js文档
	|-- main.js
|-- loaders  // 存放自定义的loader
	|-- style-loader.js  // 我们要写的loader
	|-- img-loader.js // 我们要写的loader
|-- src
	|-- assets
		|-- index.css  // 简单写的一个css样式
		|-- webpack.png  // 准备导入的图片
	|-- index.js  // 导入图片，并将图片追加在页面上
|-- package-lock.json
|-- package.json
|-- webpack.config.js  // webpack配置文件
```

```javascript
// index.js

var src = require("./assets/webpack.png")
console.log(src);
var img = document.createElement("img")
img.src = src;
document.body.appendChild(img);

```

```javascript
// img-loader.js

// 这段代码里使用了一个额外的包loader-utils，用于获取给load添加的配置options和拼接文件名

var loaderUtil = require("loader-utils")

function loader(buffer) { //给的是buffer
    console.log("文件数据大小：(字节)", buffer.byteLength);
    var { limit = 1000, filename = "[contenthash].[ext]" } = loaderUtil.getOptions(this);
    if (buffer.byteLength >= limit) {
        var content = getFilePath.call(this, buffer, filename);
    }
    else{
        var content = getBase64(buffer)
    }
    return `module.exports = \`${content}\``;
}

loader.raw = true; // 该loader要处理的是原始数据

module.exports = loader;

function getBase64(buffer) {
    return "data:image/png;base64," + buffer.toString("base64");
}

function getFilePath(buffer, name) {
    var filename = loaderUtil.interpolateName(this, name, {
        content: buffer
    });
    this.emitFile(filename, buffer);
    return filename;
}
```

```javascript
// webpack.config.js

module.exports = {
    mode: "development",
    devtool: "source-map",
    module: {
        rules: [
            {
                test: /\.(png)|(jpg)|(gif)$/, use: [{
                    loader: "./loaders/img-loader.js",
                    options: {
                        limit: 3000, //3000字节以上使用图片，3000字节以内使用base64
                        filename: "img-[contenthash:5].[ext]"
                    }
                }]
            }
        ]
    }
}
```

```javascript
// package.json

{
  "name": "test",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "loader-utils": "^1.2.3",
    "webpack": "^5.94.0",
    "webpack-cli": "^5.1.4"
  }
}
```

### 最终效果

![image-20240913144757705](./assets/webpack进阶-loader/image-20240913144757705.png)

接着将webpack.config.js中的配置大小改成10000，即10K

![image-20240913144918238](./assets/webpack进阶-loader/image-20240913144918238.png)