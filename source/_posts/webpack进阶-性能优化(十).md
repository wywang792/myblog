---
title: webpack进阶-性能优化(十)
categories:
  - 前端工程化
tags:
  - webpack
abbrlink: 50c0734e
date: 2023-08-11 12:00:00
cover:
---

# ESLint {ignore}

ESLint是一个针对JS的代码风格**检查**工具，当不满足其要求的风格时，会给予警告或错误

官网：https://eslint.org/

民间中文网：https://eslint.bootcss.com/

# 使用

ESLint通常配合编辑器使用

1. 在vscode中安装`ESLint`

该工具会自动检查工程中的JS文件

检查的工作交给`eslint`库，如果当前工程没有，则会去全局库中查找，如果都没有，则无法完成检查

另外，检查的依据是`eslint`的配置文件`.eslintrc`，如果找不到工程中的配置文件，也无法完成检查

2. 安装`eslint`

`npm i [-g] eslint`

3. 创建配置文件

可以通过`eslint`交互式命令创建配置文件

> 由于windows环境中git窗口对交互式命名支持不是很好，建议使用powershell

`npx eslint --init`

> eslint会识别工程中的`.eslintrc.*`文件，也能够识别`package.json`中的`eslintConfig`字段

# 配置

## env

配置代码的运行环境

- browser：代码是否在浏览器环境中运行
- es6：是否启用ES6的全局API，例如`Promise`等

## parser

`eslint`的工作原理是先将代码进行解析，然后按照规则进行分析

`eslint` 默认使用`Espree`作为其解析器，你可以在配置文件中指定一个不同的解析器。

## parserOptions

该配置指定`eslint`对哪些语法的支持

- ecmaVersion: 支持的ES语法版本
- sourceType
  - script：传统脚本
  - module：模块化脚本

## globals

配置可以使用的额外的全局变量

```json
{
  "globals": {
    "var1": "readonly",
    "var2": "writable"
  }
}
```

`eslint`支持注释形式的配置，在代码中使用下面的注释也可以完成配置

```js
/* global var1, var2 */
/* global var3:writable, var4:writable */
```

## extends

该配置继承自哪里

它的值可以是字符串或者数组

比如：

```json
{
  "extends": "eslint:recommended"
}
```

表示，该配置缺失的位置，使用`eslint`推荐的规则

## plugins

plugin插件主要时为eslint新增一些检查规则

## ignoreFiles

排除掉某些不需要验证的文件

`.eslintignore`

```
dist/**/*.js
node_modules
```

## rules

`eslint`规则集

每条规则影响某个方面的代码风格

每条规则都有下面几个取值：

- off 或 0 或 false: 关闭该规则的检查
- warn 或 1 或 true：警告，不会导致程序退出
- error 或 2：错误，当被触发的时候，程序会退出

除了在配置文件中使用规则外，还可以在注释中使用：

```js
/* eslint eqeqeq: "off", curly: "error" */
```

> https://eslint.bootcss.com/docs/rules/

# 示例

下面以一个vue项目的eslint配置为例。

```shell
// 安装eslint
npm install eslint --save-dev

// 安装解析vue文件需要用到的解析器和插件
npm install vue-eslint-parser eslint-plugin-vue --save-dev

// 执行修正命令，eslint中，带有扳手图标的配置项就是可以由eslint自动进行纠正的配置项
npx eslint --fix
```

在根目录下添加一个.eslintrc.js文件，配置如下

```javascript
module.exports = {
  // 指定解析器为 ESLint 解析 ES6+ 代码
  parser: "vue-eslint-parser",

  // 设置解析器选项，控制要使用的 ECMAScript 特性
  parserOptions: {
    ecmaVersion: 2020, // 支持 ES2020 的语法
    sourceType: "module", // 使用 ES 模块化
    ecmaFeatures: {
      jsx: true // 如果需要支持 JSX（一般是 Vue3 中用 Composition API 的场景）
    }
  },

  // 扩展现有的规则集或插件
  extends: [
    "eslint:recommended", // 基于 ESLint 推荐的规则
    "plugin:vue/vue3-recommended" // Vue 3 推荐的 ESLint 规则集
  ],

  // 指定代码运行的环境（例如浏览器、Node.js）
  env: {
    browser: true, // 浏览器环境
    node: true, // Node.js 环境
    es2020: true // 启用 ES2020 全局变量
  },

  // 添加自定义规则或覆盖扩展的规则
  rules: {
    "no-unused-vars": "warn", // 未使用变量仅发出警告，而不是报错
    "vue/no-mutating-props": "error", // 禁止在 Vue 中直接修改 prop
    "semi": ["error", "always"], // 强制使用分号
    "quotes": ["error", "single"], // 强制使用单引号
    "indent": ["error", 2], // 强制使用 2 空格缩进
  },

  // 引入额外的 ESLint 插件
  plugins: [
    "vue" // 允许 ESLint 校验 .vue 文件
  ],

  // 可以忽略某些文件或目录，使其不被 ESLint 校验
  ignorePatterns: ["dist/", "node_modules/"], // 忽略打包输出和依赖目录
};
```

