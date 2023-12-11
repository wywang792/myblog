---
title: git基础
date: 2022-12-26 00:00:00
categories:
  - 版本控制
tags:
  - git
abbrlink: f2204cc6
cover:
---

# 一、git的基本概念

git的4个基本概念，

- workspace：工作区
- staging area：暂存区/缓存区
- local(当地) repository：版本库或本地仓库
- remote(远程) repository：远程仓库

我们的本地修改叫做工作区，git add后添加到缓存区，git commit后添加到本地仓库，git push后推到远程仓库

# 二、git命令

1、初始化仓库

```shell
git init
```

2、配置用户信息

```shell
# 设置提交代码时的用户信息
git config --global user.name “username”          
# 设置提交代码时的用户邮箱信息
git config --global user.email “useremail”

# 如果去掉 --global 参数只对当前仓库有效
```

3、查看当前文件状态

```shell
git status
```

4、提交文件到暂存区

5、提交到本地仓库

6、提交至远程仓库

7、获取代码

8、切换分支

9、合并分支

10、回退版本 

11、关联本地代码与远程仓库