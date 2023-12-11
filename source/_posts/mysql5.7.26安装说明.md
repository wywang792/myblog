---
title: mysql5.7.26安装说明.md
date: 2020-08-10 00:00:00
categories:
  - 环境搭建
tags:
  
  - mysql
abbrlink: 
cover:
---



# mysql5.7.26安装说明

解压.zip文件

将my.ini复制到解压后文件夹路径下
修改其中的路径为自己主机的路径

my.ini文件内容

```shell

[client]
# 设置mysql客户端连接服务端时默认使用的端口
port=3306
default-character-set=utf8

[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8

[mysqld]
# 设置3306端口
port = 3306

# 设置mysql的安装目录
basedir=E:\Database\MySQL-5.7.26

# 设置 mysql数据库的数据的存放目录，MySQL 8+ 不需要以下配置，系统自己生成即可，否则有可能报错
datadir=E:\Database\MySQL-5.7.26\data

# 允许最大连接数
max_connections=20

# 服务端使用的字符集默认为8比特编码的latin1字符集
character-set-server=utf8

# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB

bind_address=0.0.0.0

# 配置MySQL允许的最大数据包大小,否则，当导入的SQL脚本过大时，可能会发生失败
max_allowed_packet=500M

#忽略主机名的方式访问
#skip-name-resolve
#跳过密码登录
#skip-grant-tables
```



配置以下环境变量（修改为自己的路径）：
MYSQL_HOME：
D:\Database\MySQL-5.7.26

在Path中新增：
%MYSQL_HOME%\bin

安装Mysql服务：
mysqld install

初始化MySQL数据库：
mysqld --initialize 

初始化后在数据库文件的路径里有一个 .err 结尾的文件
打开后在文件末尾处有root用户的密码，使用该密码初次登录

登录后初次修改root用户密码
alter user user() identified by 'root';

切换数据库
use mysql;

允许远程访问
update user set `Host` = '%' WHERE `User` = 'root' LIMIT 1;

强制刷新权限
flush privileges;


卸载MySQL服务：
sc delete mysql