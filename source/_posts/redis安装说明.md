---
title: redis3.2.100安装说明.md
date: 2020-08-10 00:00:00
categories:
  - 环境搭建
tags:
  - 数据库
  - redis
abbrlink: 
cover:
---



# redis3.2.100安装说明

解压后配置以下环境变量：

REDIS_HOME：
D:\Database\Redis-3.2.100

在Path中新增：
%REDIS_HOME%

安装Redis服务：
进入到redis的安装目录下执行以下命令：
redis-server --service-install redis.windows.conf --loglevel verbose

启动Redis服务：
redis-server --service-start
停止Redis服务：
redis-server --service-stop
卸载Redis服务：
redis-server --service-uninstall

允许远程访问：
给 redis.windows.conf 中的  bind 127.0.0.1 配置项添加注释

添加登录密码：
给 redis.windows.conf 中的  requirepass 配置项取消注释，并在其后书写想要添加的密码
