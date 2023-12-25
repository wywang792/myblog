---
title: git基础
categories:
  - 版本控制
tags:
  - git
abbrlink: 9891ebc8
date: 2023-01-11 00:00:00
cover:
---

# 一、git的基本概念

git的4个基本概念，

- **workspace**：工作区
- **staging area**：暂存区/缓存区
- **local repository**：版本库或本地仓库
- **remote repository**：远程仓库

我们的本地修改叫做工作区，git add后添加到缓存区，git commit后添加到本地仓库，git push后推到远程仓库

# 二、git命令

## 1、初始化仓库

```shell
git init
# 会在当前文件夹下生成.git隐藏文件夹，现存相关信息
```



## 2、配置用户信息

```shell
git config --global user.name “username”    
# 设置提交代码时的用户信息

git config --global user.email “useremail”
# 设置提交代码时的用户邮箱信息

# 如果去掉 --global 参数只对当前仓库有效
```



## 3、查看当前文件状态

```shell
git status
```

![image-20231211153739685](./assets/git基础/image-20231211153739685.png)



## 4、提交文件到暂存区

```shell
git add 文件名
# 将指定文件提交至暂存区

git add .
# 根据.gitignore过滤，将其余所有untrack的文件加入暂存区

git add *
# 忽略.gitignore，把所有untrack的文件加入暂存区
```



## 5、提交到本地仓库

```shell
git commit -m “备注内容”
# 提交暂存区到本地仓库中并添加备注 -m提交信息

git commit file1 file2 … -m “备注内容”    
# 提交暂存区的指定文件到仓库区

git commit --amend -m “备注信息”
# 用一次新的commit，替代上一次提交；如果代码没有任何新变化，则用来改写上一次commit的提交信息                                     
git commit --amend file1 file2 …
# 重做上一次commit，并包括指定文件的新变化
```



## 6、提交至远程仓库

git push的一般形式为 git push <远程主机名> <本地分支名> : <远程分支名>

例如 git push origin master:refs/for/master 

即是将本地的master分支推送到远程主机origin上的对应master分支

 origin 是远程主机名

第一个master是本地分支名

第二个master是远程分支名

关于 refs/for：
refs/for 的意义在于我们提交代码到服务器之后是需要经过code review 之后才能进行merge的，而refs/heads 不需要

```shell
git push origin master
# 如果远程分支被省略，如上则表示将本地分支推送到与之存在追踪关系的远程分支（通常两者同名），如果该远程分支不存在，则会被新建   

git push origin : refs/for/master
# 如果省略本地分支名，则表示删除指定的远程分支，因为这等同于推送一个空的本地分支到远程分支，等同于 git push origin –delete master

git push origin --delete branch-name
# 删除指定远程分支

git push origin
# 如果当前分支与远程分支存在追踪关系，则本地分支和远程分支都可以省略，将当前分支推送到origin主机的对应分支

git push origin --force  
# 强行推送当前分支到远程仓库，即使有冲突     

git push origin --all       
# 推送所有分支到远程仓库

git push
# 如果当前分支只有一个远程分支，那么主机名都可以省略，形如 git push，可以使用git branch -r ，查看远程的分支名
```



## 7、获取代码

```shell
git clone url [localPath]
# 将远程url仓库地址的代码拷贝到本地localPath下，localPath可省略，表示拷贝到仓库同名文件夹下
 
git clone url -b branch-name
# 拷贝远程仓库的指定分支的代码
```



## 8、切换分支

**git checkout** 用于在不同的分支之间切换、恢复文件、创建新分支等操作

```shell
git checkout <branch-name>
# 从当前分支切换到指定的分支 

git checkout -b <new-branch-name>
# 创建一个新分支 <new-branch-name> 并立即切换到新创建的分支

git checkout -b <new-branch-name> origin/<old-branch-name>
# 基于远程库分支创建分支

git checkout -
# 切换到前一个分支
```



## 9、合并分支

**git merge**可帮助将来自两个分支的更改集成到单个分支中

```shell
git merge <branch_name> 
# 将 <branch_name> 合并到当前选择的分支中。
```



## 10、回退版本 

**git reset** 命令用于回退版本，可以指定退回某一次提交的版本。

```shell
git reset [--soft | --mixed | --hard] [HEAD]
# --mixed 为默认，可以不用带该参数，用于重置暂存区的文件与上一次的提交(commit)保持一致，工作区文件内容保持不变。

git reset [HEAD] 

git reset HEAD^
# 回退所有内容到上一个版本  

git reset HEAD^ hello.php  
# 回退 hello.php 文件的版本到上一个版本  

git reset 052e           
# 回退到指定版本

--soft 
# 参数用于回退到某个版本，保留提交到本地版本库的代码：
--hard 
# 参数撤销工作区中所有未提交的修改内容，将暂存区与工作区都回到上一次版本，并删除之前的所有信息提交
# 谨慎使用--hard参数，它会删除回退点之前的所有信息。

HEAD 
# 表示当前版本

HEAD^ 
# 上一个版本

HEAD^^ 
# 上上一个版本

HEAD^^^ 
# 上上上一个版本

# 也可以使用 ～数字表示
HEAD~0
# 表示当前版本

HEAD~1 
# 上一个版本

HEAD^2 
# 上上一个版本

HEAD^3 
# 上上上一个版本
```



## 11、关联本地代码与远程仓库

```shell
git remote add <shortname> <url>
# 与远程仓库链接，shortname是给远程仓库链接添加的一个别名，方便操作

git remote add origin https://gitee.com/wywang792/myblog
# 将本地库与远程仓库https://gitee.com/wywang792/myblog建立链接，并起了一个别名，为origin

# 与远程存储库链接之后，就可以将更改推送到存储库，在推送更改之前，需要给本地的分支与远程仓库的分支建立追踪关系

git branch --track branch-name remote-branch-name                
# 新建一个分支，与指定的远程分支建立追踪关系

git branch --set-upstream branch-name remote-branch-name                   
# 建立追踪关系，在现有分支于指定的远程分支之间
```



## 12、其他命令

```shell
git log --since==2022-12-01 --until==2023-12-06 --author="wywang" --pretty=tformat: --numstat | awk '{ add += $1; subs += $2; loc += $1 - $2 } END {printf "added lines: %s, removed lines: %s, total lines: %s\n", add, subs, loc }'
# 统计代码量
```

