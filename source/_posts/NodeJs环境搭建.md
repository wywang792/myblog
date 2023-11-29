---
title: NodeJs环境搭建
date: 2020-08-19 12:00:00
categories:
  - 环境搭建
tags:
  - web
  - javascript
  - nodejs
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

# NVM_HOME值为nvm的安装路径
# NVM_SYMLINK值为nvm指定的nodejs的安装路径
# 注意path变量中添加%NVM_HOME%和%NVM_SYMLINK%时需要按顺序
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

# 当安装低版本的node时，同步安装的npm版本有可能在指定的setting配置的源中找不到。例如我当前配置的npm下载镜像是淘宝镜像。当我下载8.0.0指定的低版本node时，npm下载失败。此时，必须去掉setting里配置的镜像，让它去默认的源下载
```

## 4、nvm的相关命令：

```powershell
# 开启nodejs版本管理，第一次使用nvm时，执行该命令
nvm on

# 查看nvm版本
nvm version

# 列出所有可下载的node版本
nvm ls available

# 下载指定node版本
nvm install node版本号

# 卸载指定node版本
nvm uninstall node版本号

# 查看已下载的node版本
nvm ls

# 切换node版本
nvm use node版本号
```
# 二、安装nodejs
## 1、安装nodejs
已经安装了nvm的，可以通过nvm直接安装node

未安装nvm的，可通过nodejs官网下载安装包，然后进行安装

安装成功后，可使用以下命令进行验证

```powershell
node -v
# 可正确输出安装路径即说明安装成功
```

## 2、配置环境node环境变量

如果使用了nvm，则已经完成了环境变量的配置

如果只使用了nodejs，则需要单独配置环境变量

## 3、npm介绍

npm的全称是Node Package Manager，是一个NodeJS包管理和分发工具，方便开发者们引用其他开发者上传的开源工具包，类似Linux下的yum，Java下的Maven。

在安装node时，会自动安装对应版本的npm，比如本人安装了16.12.0版本的nodejs，自动安装了对应的8.1.0版本的npm。

## 4、npm配置

有了npm后，我们可以使用npm安装和卸载其他JS工具包。

需要注意的是，npm安装的包可分为全局包和局部包：

**全局包：**安装在默认的全局路径下，可在当前操作系统任意路径下使用；

**局部包：**只会安装在当前执行安装命令的目录的node_modules路径下，只能在当前路径及当前路径的下级路径下使用；

这里涉及到了全局包的安装路径，可通过以下命令查看：

```powershell
npm config get prefix
# 默认安装路径为C:\用户\用户名\AppData\Roming\npm
```

这种默认的路径，在使用nvm时，会导致不同版本的nodejs，npm安装各种不同版本的全局包到默认全局路径下，可能会出现各种各样的问题，因为我自己使用了nvm，所以这里修改了默认全局安装路径。

另外，缓存也是同样的问题，npm缓存是指npm在本地存储下载的软件包和依赖项的地方。当使用npm安装软件包时，它会首先检查本地缓存中是否有该软件包的副本。如果有，它将从缓存中获取软件包而不是从远程服务器下载。所以，缓存路径也同样需要修改

```powershell
# 在要配置的路径下创建文件夹，一般使用node_global，node_cache
# 本人的路径配置在D:\Language\NodeJs下，因为使用了nvm，这里的D:\Language\NodeJs实际是一个快捷方式，指向的是D:\Language\Nvm\当前使用的node版本号，这样，切换不同版本的nodejs时，全局路径及缓存路径也会相应改变

# 修改全局路径
npm config set prefix "D:\Language\NodeJs\node_global"

# 修改缓存路径
npm config set cache "D:\Language\NodeJs\node_cache"
```

修改全局路径后，为了让全局安装的包的命令可在全局路径下执行，需要配置对应的环境变量

在path中新增：

```powershell
%NVM_SYMLINK%\node_global

# 这是使用了nvm后配置的路径
# 如果是直接安装的nodejs，则添加对应的全局安装路径即可
```

除此之外，还有设置下载地址的配置需要修改，默认的下载地址为registry.npmjs.org，该地址为外网的，网速较慢，可设置为国内的镜像地址

```powershell
npm config set registry "https://registry.npmmirror.com"

# 淘宝镜像源：https://registry.npmmirror.com、https://registry.npm.taobao.org
# 腾讯镜像源：http://mirrors.cloud.tencent.com/npm/
# 华为镜像源：https://mirrors.huaweicloud.com/repository/npm/
# 官方默认镜像源：https://registry.npmjs.org
```

## 5、npm的相关命令

```powershell
-g： # --global 的缩写，表示安装到全局目录里
-S： # --save 的缩写，表示安装的包将写入package.json里面的dependencies
-D： # --save-dev 的缩写，表示将安装的包将写入packege.json里面的devDependencies
 i： # install的缩写，表示安装
 
#查看版本号
npm -v   
 
# 查看配置
npm config list

# 升级npm
npm install -g npm@9.8.1

# 清除npm的缓存
npm cache clean

# 会安装到配置的全局目录下
npm i express -g  

# 安装包信息将加入到dependencies生产依赖
npm i express -S  

# 安装包信息将加入到devDependencies开发依赖
npm i express -D  

# 卸载模块，但不卸载模块留在package.json中的对应信息
npm uninstall express 

# 卸载全局模块
npm uninstall express -g  

# 卸载模块，同时卸载留在package.json中dependencies下的信息
npm uninstall express --save  

# 卸载模块，同时卸载留在package.json中devDependencies下的信息
npm uninstall express --save-dev  

# 以临时源全局安装指定版本
npm install -g express@2.5.8 --registry=https://registry.npm.taobao.org

# 更新最新版本的express
npm update express  

# 更新到指定版本号的express
npm update express@2.1.0 

# 更新到最后的新版本
npm update express@latest

# 查看某个包对于各种包的依赖关系
npm view express dependencies

# 查看express最新的版本号
npm view express version  

# 查看所有express历史版本号（很实用）
npm view express versions 

# 查看最新的express版本的信息
npm view express  

# 查看express的详细信息，等同于上面的npm view express
npm info express  

# 查看本地已安装的express的详细信息
npm list express 或 npm ls express  

# 查看express包的来源地址
npm view express repository.url    

# 清除项目中没有被使用的包
npm prune  

# 检查模块是否已经过时
npm outdated  

# 检查模块是否已经过时
npm outdated 

# 会打开默认浏览器跳转到github中express的页面
npm repo express  

# 会打开默认浏览器跳转到github中express的README.MD文件信息
npm docs express 

# 会打开默认浏览器跳转到github中express的主页
npm home express  

```