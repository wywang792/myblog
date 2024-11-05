---
title: webpack进阶(十一)-bundle analyzer
categories:
  - 前端工程化
tags:
  - webpack
abbrlink: 2c680859
date: 2023-08-13 12:00:00
cover:
---

# bundle analyzer

WebpackBundleAnalyzer，使用该插件，对项目进行编译打包后，会自动启动一个http服务，打开该服务监听的端口，能够查看打包出的文件大小和占比情况，如下图：

我们可以通过分析最终的打包结果，对文件进行针对性处理。

比如将独立模块进行分包，或者压缩，或者替换更加轻量化的框架模块。

![image-20240918005410486](./assets/webpack进阶-性能优化(十一)/image-20240918005410486.png)

![image-20240918005439513](./assets/webpack进阶-性能优化(十一)/image-20240918005439513.png)