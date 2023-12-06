---
title: node-sass安装问题处理
date: 2022-12-06 12:00:00
categories:
  - 常见问题
tags:
  - javascript
  - nodejs
abbrlink: 8d194d53
---

# 安装node-sass时会遇到的问题

主要会出现以下三个问题：

1、下载node-sass二进制包时下载失败；

2、提示需要安装Python27；

3、安装windows-build-tools，卡在Still waiting for installer log file... 

以下就三个问题给出解决方案：

## 问题一：下载node-sass二进制包时下载失败

**问题原因：**下载包的地址被和谐了

**解决方案：**修改下载地址

**具体操作：**

方法1：在安装node-sass包的时候，使用以下命令修改下载地址

```shell
npm i node-sass --sass_binary_site=https://npm.taobao.org/mirrors/node-sass/
```

方法2：给系统添加环境变量：

```shell
// linux、mac 下
SASS_BINARY_SITE=https://npm.taobao.org/mirrors/node-sass/ npm install node-sass

// window 下
set SASS_BINARY_SITE=https://npm.taobao.org/mirrors/node-sass/ && npm install node-sass
```

方法3：给npm添加配置项后安装node-sass

```shell
npm config set sass_binary_site https://npm.taobao.org/mirrors/node-sass/
npm i node-sass
```

## 问题二：提示需要安装Python27

**问题原因：**node-sass的代码主要是C++实现，运行node-sass需要进行编译后再运行，而python可以为node-sass提供运行时编译环境，所以正常情况下下载node-sass源代码同时会检查python的安装，确保正常运行的环境

**解决方案：**下载Python2.7进行安装，并设置环境变量

## 问题三：安装windows-build-tools，卡在Still waiting for installer log file... 

**问题原因：**windows-build-tools其实已经下载好了，但是不能自己进行安装

**解决方案：**手动安装windows-build-tools

**具体操作：**

打开文件：C:\Users\\{用户名}\\windows-build-tools\vs_BuildTools.exe

选择->单个组件->勾选Node.js MSBuild 支持，下载


