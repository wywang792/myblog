---
title: hexo启动失败问题处理
date: 2023-05-06 14:23:04
categories:
  - 博客搭建
tags:
  - hexo
abbrlink: 3d513d05
cover:
---
  
# 执行hexo server报错

如图：

![image-20230113152815193](./assets/hexo启动失败问题处理/image-20230113152815193.png)

**问题原因：**hexo sever命令会执行hexo.ps1这个脚本，该脚本为powershell脚本，在windows系统下，我们无法执行powershell脚本

**解决方案：**修改powershell脚本执行策略

**具体操作：**

设置->隐私和安全性->开发者选项->允许本地PowerShell脚本在为签名的情况下运行

![image-20230113153322329](./assets/hexo启动失败问题处理/image-20230113153322329.png)