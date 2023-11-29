---
title: Node环境搭建
date: 2020-08-19 12:00:00
categories:
  - 环境搭建
tags:
  - web
  - javascript
abbrlink: 3f14f460
---
# 一、安装nvm
在Web项目开发中，由于各种前端框架、插件 以及 Nodejs、Npm 的飞速更新，在开发新项目或对老项目进行更新维护时，有些项目版本的配置与当前Node、Npm环境不匹配，导致运行报错，甚至无法启动。

nvm 是 Node Version Manager 的缩写，是一个用于在同一台计算机上安装和管理多个 Node.js 版本的工具。它允许你轻松地在不同的项目中使用不同版本的 Node.js，而不必全局更改你的 Node.js 安装。

## 1、下载官方安装包进行安装
下载地址：https://github.com/coreybutler/nvm-windows/releases

windows系统下，下载nvm-setup.exe即可

进入安装界面后，分别选择nvm安装路径及nodejs安装路径

这里的nodejs安装路径的选择，实际上会生成一个快捷方式的链接，真实的nodejs仍然会被下载在nvm的安装路径中，这是为了日后在切换node版本时，nvm只需要改变该快捷方式的链接路径，而无需修改其他任何东西

本人选择的安装路径：
nvm路径：D:\Language\Nvm
node路径：D:\Language\nodejs

## 2、配置nvm环境变量
安装nvm时，会自动配置环境变量，检查如果未配置，则手动进行配置
windows系统下，在系统变量中添加：

```powershell
NVM_HOME:
D:\Language\Nvm

NVM_SYMLINK:
D:\Language\nodejs

// NVM_HOME值为nvm的安装路径
// NVM_SYMLINK值为nvm指定的nodejs的安装路径
// 注意path变量中添加%NVM_HOME%和%NVM_SYMLINK%时需要按顺序
```

在path中添加如下路径：

```powershell
%NVM_HOME%
%NVM_SYMLINK%
```

## 3、配置下载镜像地址
由于默认的镜像地址为registry.npmjs.org是外网的，下载node安装包及依赖包非常慢，所以我们把镜像配置成国内的，加快下载速度。
在nvm安装路径中找到setting.txt配置文件，添加node_mirror、npm_mirror：

```powershell
node_mirror: https://npmmirror.com/mirrors/node/
npm_mirror: https://npmmirror.com/mirrors/npm/

// 当安装低版本的node时，同步安装的npm版本有可能在指定的setting配置的源中找不到。例如我当前配置的npm下载镜像是淘宝镜像。当我下载8.0.0指定的低版本node时，npm下载失败。此时，必须去掉setting里配置的镜像，让它去默认的源下载
```

## 4、nvm的相关命令：

```powershell
第一次使用nvm时，执行该命令
// nvm on

// 查看nvm版本
nvm version

// 列出所有可下载的node版本
nvm ls available

// 下载指定node版本
nvm install node版本号

// 卸载指定node版本
nvm uninstall node版本号

// 查看已下载的node版本
nvm ls

// 切换node版本
nvm use node版本号
```
# 二、安装nodejs
## 1、安装nodejs
已经安装了nvm的，可以通过nvm直接安装node
未安装nvm的，可通过nodejs官网下载安装包，然后进行安装

## 2、npm的相关命令：

```powershell
# 查看配置
npm config list

# 修改下载源为淘宝镜像
npm config set registry https://registry.npm.taobao.org

# 使用淘宝镜像下载cnpm，以后用cnpm进行安装
npm install -g cnpm --registry=https://registry.npm.taobao.org
# sass文件进行
npm config set  sass_binary_site=https://npm.taobao.org/mirrors/node-sass
# electron开发用的镜像
npm config set  ELECTRON_MIRROR=https://npm.taobao.org/mirrors/electron

# 查看全局下载的路径
npm config get prefix

# 修改全局下载的路径
npm config set prefix "C:\Program Files\nodejs\node_global"
# 需要同时修改缓存位置
npm config set cache "C:\Program Files\nodejs\node_cache"

# 如果修改了全局下载路径后，在命令行下提示命令不存在，则设置环境变量
# 添加NODE_HOME
NODE_HOME=C:\Program Files\nodejs
# 在Path中添加其他环境变量
%NODE_HOME%
%NODE_HOME%\node_global
%NODE_HOME%\node_modules
```