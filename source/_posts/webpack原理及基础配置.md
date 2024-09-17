---
title: webpack原理及基础配置
categories:
  - 前端工程化
tags:
  - webpack
abbrlink: 4edc021
date: 2023-07-03 12:00:00
cover:
---



## 一、webpack出现的原因

### 浏览器端的模块化

问题：

- 效率问题：精细的模块划分带来了更多的JS文件，更多的JS文件带来了更多的请求，降低了页面访问效率
- 兼容性问题：浏览器目前仅支持ES6的模块化标准，并且还存在兼容性问题
- 工具问题：浏览器不支持npm下载的第三方包

这些仅仅是前端工程化的一个缩影

当开发一个具有规模的程序，你将遇到非常多的非业务问题，这些问题包括：执行效率、兼容性、代码的可维护性可扩展性、团队协作、测试等等等等，我们将这些问题称之为工程问题。工程问题与业务无关，但它深刻的影响到开发进度，如果没有一个好的工具解决这些问题，将使得开发进度变得极其缓慢，同时也让开发者陷入技术的泥潭。

### 根本原因

思考：上面提到的问题，为什么在node端没有那么明显，反而到了浏览器端变得如此严重呢？

答：在node端，运行的JS文件在本地，因此可以本地读取文件，它的效率比浏览器远程传输文件高的多

**根本原因**：在浏览器端，开发时态（devtime）和运行时态（runtime）的侧重点不一样

**开发时态，devtime：**

1. 模块划分越细越好
2. 支持多种模块化标准
3. 支持npm或其他包管理器下载的模块
4. 能够解决其他工程化的问题

**运行时态，runtime：**

1. 文件越少越好
2. 文件体积越小越好
3. 代码内容越乱越好
4. 所有浏览器都要兼容
5. 能够解决其他运行时的问题，主要是执行效率问题

这种差异在小项目中表现的并不明显，可是一旦项目形成规模，就越来越明显，如果不解决这些问题，前端项目形成规模只能是空谈

### 解决办法

既然开发时态和运行时态面临的局面有巨大的差异，因此，我们需要有一个工具，这个工具能够让开发者专心的在开发时态写代码，然后利用这个工具将开发时态编写的代码转换为运行时态需要的东西。

这样的工具，叫做**构建工具**

![image-20240910092338192](./assets/webpack原理及基础配置/image-20240910092338192.png)

这样一来，开发者就可以专注于开发时态的代码结构，而不用担心运行时态遇到的问题了。

### 常见的构建工具

- **webpack**
- grunt
- gulp
- browserify
- fis
- 其他

## 二、webpack简介

webpack是基于模块化的打包（构建）工具，它把一切视为模块

它通过一个开发时态的入口模块为起点，分析出所有的依赖关系，然后经过一系列的过程（压缩、合并），最终生成运行时态的文件。

webpack的特点：

- **为前端工程化而生**：webpack致力于解决前端工程化，特别是浏览器端工程化中遇到的问题，让开发者集中注意力编写业务代码，而把工程化过程中的问题全部交给webpack来处理
- **简单易用**：支持零配置，可以不用写任何一行额外的代码就使用webpack
- **强大的生态**：webpack是非常灵活、可以扩展的，webpack本身的功能并不多，但它提供了一些可以扩展其功能的机制，使得一些第三方库可以融于到webpack中
- **基于nodejs**：由于webpack在构建的过程中需要读取文件，因此它是运行在node环境中的
- **基于模块化**：webpack在构建过程中要分析依赖关系，方式是通过模块化导入语句进行分析的，它支持各种模块化标准，包括但不限于CommonJS、ES6 Module

![image-20240910092338191](./assets/webpack原理及基础配置/image-20240910092259819.png)

## 三、webpack安装

webpack通过npm安装，它提供了两个包：

- webpack：核心包，包含了webpack构建过程中要用到的所有api
- webpack-cli：提供一个简单的cli命令，它调用了webpack核心包的api，来完成构建过程

安装方式：

- 全局安装：可以全局使用webpack命令，但是无法为不同项目对应不同的webpack版本
- **本地安装**：推荐，每个项目都使用自己的webpack版本进行构建

```shell
# 初始化npm
npm init

# 安装
npm i -D webpack webpack-cli

# 使用
npx webpack

# npx说明：npx是npm自5.2.0版本引入的命令，用于执行npm包中的可执行文件，如果不使用npx，则需要在package.json的scripts属性中书写相关命令，使用 npm run 进行执行

# 默认情况下，webpack会以`./src/index.js`作为入口文件分析依赖关系，打包到`./dist/main.js`文件中

# 通过--mode选项可以控制webpack的打包结果的运行环境
# development：开发环境，默认开启source map，不压缩代码，方便开发调试
# production：生产环境，默认关闭source map，会压缩代码，减少文件体积，提升加载性能
```

## 四、webpack编译结果分析

### 初始化的项目结构

```
|-- webpack
	|-- node_modules
	|-- src
		|-- a.js
		|-- index.js
	|--package-lock.json
	|--package.json
```



```javascript
// src/a.js

console.log("this is a");
module.exports = {
  a: "a",
};
```



```javascript
// src/index.js

console.log("Hello, Webpack");
```



```json
// package.json

{
  "name": "webpack",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "webpack": "^5.94.0",
    "webpack-cli": "^5.1.4"
  }
}
```

### 编译后的项目结构

```shell
# 进行编译
npx webpack --mode=development
```

```
|-- webpack
	|-- dist
		|-- main.js
	|-- node_modules
	|-- src
		|-- index.js
	|--package-lock.json
	|--package.json
```

```javascript
// dist/main.js

/*
 * ATTENTION: The "eval" devtool has been used (maybe by default in mode: "development").
 * This devtool is neither made for production nor for readable output files.
 * It uses "eval()" calls to create a separate source file in the browser devtools.
 * If you are trying to read the output file, select a different devtool (https://webpack.js.org/configuration/devtool/)
 * or disable the default devtool with "devtool: false".
 * If you are looking for production-ready output files, see mode: "production" (https://webpack.js.org/configuration/mode/).
 */
/******/ (() => { // webpackBootstrap
/******/ 	var __webpack_modules__ = ({

/***/ "./src/a.js":
/*!******************!*\
  !*** ./src/a.js ***!
  \******************/
/***/ ((module) => {

eval("console.log(\"this is a\");\r\nmodule.exports = {\r\n  a: \"a\",\r\n};\r\n\n\n//# sourceURL=webpack://webpack/./src/a.js?");

/***/ }),

/***/ "./src/index.js":
/*!**********************!*\
  !*** ./src/index.js ***!
  \**********************/
/***/ ((__unused_webpack_module, __unused_webpack_exports, __webpack_require__) => {

eval("__webpack_require__(/*! ./a */ \"./src/a.js\");\r\nconsole.log(\"Hello, Webpack\");\r\n\n\n//# sourceURL=webpack://webpack/./src/index.js?");

/***/ })

/******/ 	});
/************************************************************************/
/******/ 	// The module cache
/******/ 	var __webpack_module_cache__ = {};
/******/ 	
/******/ 	// The require function
/******/ 	function __webpack_require__(moduleId) {
/******/ 		// Check if module is in cache
/******/ 		var cachedModule = __webpack_module_cache__[moduleId];
/******/ 		if (cachedModule !== undefined) {
/******/ 			return cachedModule.exports;
/******/ 		}
/******/ 		// Create a new module (and put it into the cache)
/******/ 		var module = __webpack_module_cache__[moduleId] = {
/******/ 			// no module.id needed
/******/ 			// no module.loaded needed
/******/ 			exports: {}
/******/ 		};
/******/ 	
/******/ 		// Execute the module function
/******/ 		__webpack_modules__[moduleId](module, module.exports, __webpack_require__);
/******/ 	
/******/ 		// Return the exports of the module
/******/ 		return module.exports;
/******/ 	}
/******/ 	
/************************************************************************/
/******/ 	
/******/ 	// startup
/******/ 	// Load entry module and return exports
/******/ 	// This entry module can't be inlined because the eval devtool is used.
/******/ 	var __webpack_exports__ = __webpack_require__("./src/index.js");
/******/ 	
/******/ })()
;
```

### 编译结果分析

我这里首先删除掉多余的注释后代码如下：

```javascript
(() => {
  // webpackBootstrap
  var __webpack_modules__ = {
    "./src/a.js": (module) => {
      eval(
        'console.log("this is a");\r\nmodule.exports = {\r\n  a: "a",\r\n};\r\n\n\n//# sourceURL=webpack://webpack/./src/a.js?'
      );
    },

    "./src/index.js": (
      __unused_webpack_module,
      __unused_webpack_exports,
      __webpack_require__
    ) => {
      eval(
        '__webpack_require__(/*! ./a */ "./src/a.js");\r\nconsole.log("Hello, Webpack");\r\n\n\n//# sourceURL=webpack://webpack/./src/index.js?'
      );
    },
  };
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
  var __webpack_exports__ = __webpack_require__("./src/index.js");
})();

```

**代码说明：**

1. 最外层是一个无参数的立即执行函数；
2. 声明了\_\__webpack_modules\_\__对象
3. \_\_webpack_modules\_\_对象有两个属性，"./src/a.js"，"./src/index.js"，均为函数
4. "./src/a.js"函数参数为module，内容为eval执行我们原本写的代码，并且在最后位置添加了sourceURL
5. "./src/a.js"函数参数为\_\_unused_webpack_module\_\_，\_\_unused_webpack_exports\_\_，\_\_webpack_require\_\_，内容为eval执行我们原本写的代码，并且在最后位置添加了sourceURL
6. 声明了\_\_webpack_module_cache\_\_对象
7. 声明了\_\_webpack_require\_\_函数，参数为moduleId
8. 声明了cachedModule对象，从\_\_webpack_module_cache\_\_对象中根据moduleId获取，如果已经存在，则直接返回
9. 声明了module对象，括号括起来，在javascript中表示立即执行该变量所代表的函数表达式，这里的具体含义为，创建了一个新的对象，其中有exorts属性的空对象，将这个对象赋值给\_\_webpack_module_cache\_\_，同时赋值给module
10. 执行对应moduleId的module中的代码，传入参数为module，module.exports， \_\_webpack_require\_\_
11. 返回module.exports
12. 声明了\_\_webpack_exports\_\_变量，其结果为调用\_\_webpack_require\_\_("./src/index.js")的结果，这里声明的变量最终并没有什么用处，主要目的是执行\_\_webpack_require\_\_函数



经过编译后，将我们的代码进行了转换，通过立即执行函数，内部通过加载module，缓存module，执行module等实现了将多模块代码打包成单js文件的目的。

**代码解释：**

首先，创建了立即执行函数，目的是不造成全局污染，并且执行内部代码

在代码中声明\_\_webpack_require\_\_函数并执行\_\_webpack_require\_\_("./src/index.js")，从入口文件开始执行代码

在\_\_webpack_require\_\_中，我们首先从缓存中根据文件路径获取module，如果缓存中有，则返回module的exports，如果没有对应的module，那我们创建一个module，存入到缓存中，并且通过执行对应module的代码，将这段代码的最终执行结果存入到module的exports属性中。

代码量很少，但代码最终实现的效果是非常强大的，这种思路非常的Nice，很值得学习。

## 五、编译过程

webpack 的作用是将源代码编译（构建、打包）成最终代码

![image-20240910113355520](./assets/webpack原理及基础配置/image-20240910113355520.png)

整个过程大致分为三个步骤

1. 初始化
2. 编译
3. 输出

![image-20240910113446030](./assets/webpack原理及基础配置/image-20240910113446030.png)

### 初始化

此阶段，webpack会将**CLI参数**、**配置文件**、**默认配置**进行融合，形成一个最终的配置对象。

对配置的处理过程是依托一个第三方库`yargs`完成的

此阶段相对比较简单，主要是为接下来的编译阶段做必要的准备

目前，可以简单的理解为，初始化阶段主要用于产生一个最终的配置

### 编译

1、**创建chunk**

chunk是webpack在内部构建过程中的一个概念，译为`块`，它表示通过某个入口找到的所有依赖的统称。

根据入口模块（默认为`./src/index.js`）创建一个chunk

![image-20240910113635687](./assets/webpack原理及基础配置/image-20240910113635687.png)

每个chunk都有至少两个属性：

- name：默认为main
- id：唯一编号，开发环境和name相同，生产环境是一个数字，从0开始

2、**构建所有依赖模块**

![image-20240910113716852](./assets/webpack原理及基础配置/image-20240910113716852.png)

3、**产生chunk assets**

在第二步完成后，chunk中会产生一个模块列表，列表中包含了**模块id**和**模块转换后的代码**

接下来，webpack会根据配置为chunk生成一个资源列表，即`chunk assets`，资源列表可以理解为是生成到最终文件的文件名和文件内容

![image-20240910113916811](./assets/webpack原理及基础配置/image-20240910113916811.png)

> chunk hash是根据所有chunk assets的内容生成的一个hash字符串
>
> hash：一种算法，具体有很多分类，特点是将一个任意长度的字符串转换为一个固定长度的字符串，而且可以保证原始内容不变，产生的hash字符串就不变
>
> 总共有三个hash：
>
> hash：所有的chunk合并在一起，产生的总的hash
>
> chunkhash：单个chunk的内容，产生的hash
>
> contenthash：单个模块的内容，产生的hash

4、**合并chunk assets**

将多个chunk的assets合并到一起，并产生一个总的hash。

注：一个chunk最终生成的可能并不止一个文件，除了js文件外，还会包含所依赖的css、jpg等静态资源文件。

![image-20240910114151500](./assets/webpack原理及基础配置/image-20240910114151500.png)

### 输出

此步骤非常简单，webpack将利用node中的fs模块（文件处理模块），根据编译产生的总的assets，生成相应的文件。

### 总过程

![image-20240910114309828](./assets/webpack原理及基础配置/image-20240910114309828.png)

![image-20240910114320044](./assets/webpack原理及基础配置/image-20240910114320044.png)

### **涉及术语**

1. module：模块，分割的代码单元，webpack中的模块可以是任何内容的文件，不仅限于JS
2. chunk：webpack内部构建模块的块，一个chunk中包含多个模块，这些模块是从入口模块通过依赖分析得来的
3. bundle：chunk构建好模块后会生成chunk的资源清单，清单中的每一项就是一个bundle，可以认为bundle就是最终生成的文件
4. hash：最终的资源清单所有内容联合生成的hash值
5. chunkhash：chunk生成的资源清单内容联合生成的hash值
6. chunkname：chunk的名称，如果没有配置则使用main
7. id：通常指chunk的唯一编号，如果在开发环境下构建，和chunkname相同；如果是生产环境下构建，则使用一个从0开始的数字进行编号

## 六、配置文件

webpack提供的cli支持很多的参数，例如`--mode`，但更多的时候，我们会使用更加灵活的配置文件来控制webpack的行为

默认情况下，webpack会读取`webpack.config.js`文件作为配置文件，但也可以通过CLI参数`--config`来指定某个配置文件

配置文件中通过CommonJS模块导出一个对象，对象中的各种属性对应不同的webpack配置

**注意：配置文件中的代码，必须是有效的node代码**

当命令行参数与配置文件中的配置出现冲突时，以命令行参数为准。

**基本配置：**

1. mode：编译模式，字符串，取值为development或production，指定编译结果代码运行的环境，会影响webpack对编译结果代码格式的处理
2. entry：入口，指定入口文件
3. output：出口，指定编译结果文件

## 七、devtool配置

### source map 源码地图

> 本小节的知识与 webpack 无关

前端发展到现阶段，很多时候都不会直接运行源代码，可能需要对源代码进行合并、压缩、转换等操作，真正运行的是转换后的代码。

![image-20240910115139222](./assets/webpack原理及基础配置/image-20240910115139222.png)

这就给调试带来了困难，因为当运行发生错误的时候，我们更加希望能看到源代码中的错误，而不是转换后代码的错误，为了解决这一问题，chrome浏览器率先支持了source map，其他浏览器纷纷效仿，目前，几乎所有新版浏览器都支持了source map。

source map在webpack中实际上只需要开启一个配置，配置开启后，编译生成最终文件时，不仅会生成所有源码内容，还会生成记录了和转换后的代码的对应关系的map文件。

下面是浏览器处理source map的原理：

![image-20240910115416336](./assets/webpack原理及基础配置/image-20240910115416336.png)

![image-20240910115441132](./assets/webpack原理及基础配置/image-20240910115441132.png)

### 最佳实践

1. source map 应在开发环境中使用，作为一种调试手段。
2. source map 不应该在生产环境中使用，source map的文件一般较大，不仅会导致额外的网络传输，还容易暴露原始代码。即便要在生产环境中使用source map，用于调试真实的代码运行问题，也要做出一些处理规避网络传输和代码暴露的问题。

### webpack中的source map

使用 webpack 编译后的代码难以调试，可以通过 devtool 配置来优化调试体验。

## 八、入口和出口

```js
module.exports = {
    entry:{
        pageA: "./src/pageA/index.js",
        pageB: "./src/pageB/index.js",
        pageC: ["./src/pageC/main1.js", "./src/pageC/main2.js"]
    },
    output:{
        // path需要指定绝对路径，使用nodejs的path模块
      	path: path.resolve(__dirname, 'dist')
        filename:"[name].[chunkhash:5].js"
    }
}
```



### **入口**

**入口真正配置的是chunk**

入口通过entry进行配置

entry可以使用字符串，也可以使用数组

字符串时：单入口文件，最终打包成一个js，最终只执行一个函数，如：\_\_webpack_require\_\_("./src/index.js")

数组时：多入口文件，最终打包也是一个js，但最终打包出来的js中，会执行两个\_\_webpack_require\_\_函数

![image-20240910163203776](./assets/webpack原理及基础配置/image-20240910163203776.png)

### 出口

这里的出口是针对资源列表的文件名或路径的配置

出口通过output进行配置

规则：

- name：chunkname
- fullhash： 总的资源hash
- chunkhash： chunk文件的hash
- contenthash：module文件内容的hash
- id：使用chunkid，不推荐
- 哈希值，可以使用冒号后跟数字，表示将哈希值截取指定位数，如chunkhash:5标识截取chunkhash的前五位

### 入口和出口的最佳实践



#### 一个页面一个JS

![image-20240910160412406](./assets/webpack原理及基础配置/image-20240910160412406.png)

源码结构

```
|—— src
    |—— pageA   页面A的代码目录
        |—— index.js 页面A的启动模块
        |—— ...
    |—— pageB   页面B的代码目录
        |—— index.js 页面B的启动模块
        |—— ...
    |—— pageC   页面C的代码目录
        |—— main1.js 页面C的启动模块1 例如：主功能
        |—— main2.js 页面C的启动模块2 例如：实现访问统计的额外功能
        |—— ...
    |—— common  公共代码目录
        |—— ...
```

webpack配置

```js
module.exports = {
    entry:{
        pageA: "./src/pageA/index.js",
        pageB: "./src/pageB/index.js",
        pageC: ["./src/pageC/main1.js", "./src/pageC/main2.js"]
    },
    output:{
        filename:"[name].[chunkhash:5].js"
    }
}
```

这种方式适用于页面之间的功能差异巨大、公共代码较少的情况，这种情况下打包出来的最终代码不会有太多重复

#### 一个页面多个JS

![image-20240910160446925](./assets/webpack原理及基础配置/image-20240910160446925.png)

源码结构

```
|—— src
    |—— pageA   页面A的代码目录
        |—— index.js 页面A的启动模块
        |—— ...
    |—— pageB   页面B的代码目录
        |—— index.js 页面B的启动模块
        |—— ...
    |—— statistics   用于统计访问人数功能目录
        |—— index.js 启动模块
        |—— ...
    |—— common  公共代码目录
        |—— ...
```

webpack配置

```js
module.exports = {
    entry:{
        pageA: "./src/pageA/index.js",
        pageB: "./src/pageB/index.js",
        statistics: "./src/statistics/index.js"
    },
    output:{
        filename:"[name].[chunkhash:5].js"
    }
}
```

这种方式适用于页面之间有一些**独立**、相同的功能，专门使用一个chunk抽离这部分JS有利于浏览器更好的缓存这部分内容。

> 思考：为什么不使用多启动模块的方式？

#### 单页应用

所谓单页应用，是指整个网站（或网站的某一个功能块）只有一个页面，页面中的内容全部靠JS创建和控制。 vue和react都是实现单页应用的利器。

![image-20240910160549079](./assets/webpack原理及基础配置/image-20240910160549079.png)

源码结构

```
|—— src
    |—— subFunc   子功能目录
        |—— ...
    |—— subFunc   子功能目录
        |—— ...
    |—— common  公共代码目录
        |—— ...
    |—— index.js
```

webpack配置

```js
module.exports = {
    entry: "./src/index.js",
    output:{
        filename:"index.[fullhash:5].js"
    }
}
```



