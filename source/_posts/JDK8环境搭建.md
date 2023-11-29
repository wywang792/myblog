---
title: JDK8环境搭建
date: 2019-07-28 12:00:00
categories:
  - 环境搭建
tags:
  - java
abbrlink: 4a17b156
---
## 1、使用官方安装包进行安装

## 2、配置环境变量
windows系统下，在系统变量中添加：

```powershell
JAVA_HOME:
C:\Program Files\Java\jdk1.8.0_221

CLASSPATH:
.;%JAVA_HOME%\lib;%JAVA_HOME%\lib\tools.jar
```

在path中添加如下路径：

```powershell
%JAVA_HOME%\bin
%JAVA_HOME%\jre\bin
```

## 3、验证
在cmd窗口中输入：

```powershell
java -version

// 能够正确输出如下信息
java version "1.8.0_221"
Java(TM) SE Runtime Environment (build 1.8.0_221-b11)
Java HotSpot(TM) 64-Bit Server VM (build 25.221-b11, mixed mode)
// 说明安装成功
```
