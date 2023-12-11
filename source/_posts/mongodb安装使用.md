---
title: mongodb安装使用.md
date: 2020-12-18 14:23:04
categories:
  - 环境搭建
tags:
  - 数据库
  - mongodb
abbrlink: 
cover:
---

# 一、MongoDB的下载与安装

## 1、下载安装包

可以从官网下载最新版本，也可以从以下网址下载历史版本

官网：<https://www.mongodb.com/download-center/community>

历史版本：http://dl.mongodb.org/dl/win32/x86_64

Ctrl + F 查找自己想要的版本，进行下载

![Snipaste_2019-04-27_17-34-02](assets/mongodb安装使用//Snipaste_2019-04-27_17-34-02.png)

## 2、开始安装

点击next进行，下面列出需要进行选择的一些界面。

![Snipaste_2019-04-27_17-36-18](assets/mongodb安装使用//Snipaste_2019-04-27_17-36-18.png)

![Snipaste_2019-04-27_17-38-38](assets/mongodb安装使用//Snipaste_2019-04-27_17-38-38.png)

![Snipaste_2019-04-27_17-40-05](assets/mongodb安装使用//Snipaste_2019-04-27_17-40-05.png)

![Snipaste_2019-04-27_17-42-28](assets/mongodb安装使用//Snipaste_2019-04-27_17-42-28.png)

## 3、安装完成，进行配置

在安装的bin目录下有 **mongod.cfg** 文件，这是一个配置文件，需要操作的配置都写在这里边。

配置项官方文档：<https://docs.mongodb.com/manual/reference/configuration-options/#security-options>

![Snipaste_2019-04-27_17-44-11](assets/mongodb安装使用//Snipaste_2019-04-27_17-44-11.png)

## 4、配置允许远程访问

```yaml
net:
  port: 27017
  bindIp: 0.0.0.0
```

把 bindIp：127.0.0.1 修改为 bindIp：0.0.0.0 ，然后重启MongoDB服务。

## 5、配置用户

MongDB默认是没有用户的，可以直接登录。给MongoDB配置初始的用户并开启认证模式。

先登录到MongoDB。

需要先创建一个帐号，该账号需要有grant权限，即：账号管理的授权权限。注意一点，帐号是跟着库走的，所以在指定库里授权(也就是使用这个账号创建用户时，这个用户要管理哪个数据库，那就先选择数据库，在对应的数据库下面去创建用户)，必须也在指定库里验证(auth)。

选择数据库并添加用户：

```
#选择admin数据库
use admin

#创建带有授权权限的用户
db.createUser({user:"root",pwd: "root",roles:[{role:"root",db:"admin"}]})

#退出
quit()

#在配置文件中加上以下配置，然后重新启动服务
security:
  authorization: enabled
  
#使用创建的root用户登录
mongo -uroot -p

#选择gwnapmt数据库
use gwnapm

#创建一个临时集合并插入一条数据，否则退出时，该数据库会被删除
db.test.save({"_id": 1});

#创建管理和使用gwnapm数据库的gwnapm用户
db.createUser({user:"gwnapm",pwd: "gwnapm",roles:[{role:"dbOwner",db:"gwnapm"},{role:"readWrite",db:"gwnapm"}]})

#查询用户的命令
use admin
#pretty()函数是美化查询结果
db.system.users.find().pretty()

#pmstat用户连接数据库，需要使用[authenticationDatabase]参数去认证对应的数据库
mongo --host 192.168.0.33 --port 27017 -u gwnapm -authenticationDatabase gwnapm -p
```

```
user：用户名

pwd：密码

roles：指定用户的角色，可以用一个空数组给新用户设定空角色；
在roles字段,可以指定内置角色和用户定义的角色。role里的角色可以选：
1、数据库用户角色
	read：允许用户读取指定数据库
	readWrite：允许用户读写指定数据库
2、数据库管理角色：
	dbAdmin：允许用户在指定数据库中执行管理函数，如索引创建、删除，查看统计或访问system.profile
	dbOwner：
	userAdmin：允许用户向system.users集合写入，可以在指定数据库里创建、删除和管理用户
3、集群管理角色
	clusterAdmin：只在admin数据库中可用，赋予用户所有分片和复制集相关函数的管理权限
	clusterManager：
	clusterMonitor：
	hostManager：
4、备份恢复角色
	backup：
	restore：
5、所有数据库角色
	readAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的读权限
	readWriteAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的读写权限
	userAdminAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的userAdmin权限
	dbAdminAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的dbAdmin权限
6、超级用户角色
	root：只在admin数据库中可用。超级账号，超级权限
7、内部角色
	__system:
```



# 二、MongoDB的基本命令

## 1、连接MongoDB

mongo [options]

| 参数                     | 其他用法   | 默认值    | 说明       |
| ------------------------ | ---------- | --------- | ---------- |
| --host                   |            | 127.0.0.1 | 主机地址   |
| --port                   |            | 27017     | 主机端口   |
| -u                       | --username |           | 用户名     |
| -p                       | --password |           | 密码       |
| --authenticationDatabase |            |           | 验证数据库 |

例:	mongo --host 192.168.0.33 --port 27017 -u sqlmonit -p sqlmonit 

--authenticationDatabase sqlmonit

## 2、MongoDB数据导出

### （1）mongodump

| 参数   | 其他用法     | 默认值    | 说明       |
| ------ | ------------ | --------- | ---------- |
| -h     | --host       | 127.0.0.1 | 主机地址   |
| --port |              | 27017     | 主机端口   |
| -u     | --username   |           | 用户名     |
| -p     | --password   |           | 密码       |
| -d     | --db         |           | 数据库实例 |
| -c     | --collection |           | 集合       |
| -o     | --out        |           | 输出路径   |

例：	mongodump -h 127.0.0.1:27017 -d sqlmonit -u sqlmonit -p sqlmonit 

-o C:\Users\wyWan\Desktop 

### （2）mongoexport

这个可以导出csv和json格式的数据

| 参数        | 其他用法 | 默认值 | 说明                                  |
| ----------- | -------- | ------ | ------------------------------------- |
| -h          |          |        | 主机地址                              |
| --port      |          |        | 主机端口                              |
| -u          |          |        | 用户名                                |
| -p          |          |        | 密码                                  |
| -d          |          |        | 数据库实例                            |
| -c          |          |        | 集合                                  |
| -o          |          |        | 输出路径                              |
| --csv       |          |        | 导出CSV格式                           |
| -f          |          |        | 导出哪些字段，**导出CSV格式时必须带** |
| --jsonArray |          |        | 导出JSON格式                          |
| --query     |          |        | 相当于给SQL加where条件                |

例： 



mongoexport -d pmstat -u pmstat -p pmstat -c 20190624_apm_sql --csv -f orgi_sql,use_time_ms --query {"use_time_ms":{"$gt":3000}}  -o d:/sql.csv

导出为json格式：

mongoexport -d pmstat -u pmstat -p pmstat -c 20200715_apm_sql --jsonArray -o ./export/20200715_apm_sql.json

## 3、MongoDB数据导入

### （1）mongorestore

mongorestore [options]

| 参数   | 其他用法   | 默认值    | 说明                                           |
| ------ | ---------- | --------- | :--------------------------------------------- |
| -h     | --host     | 127.0.0.1 | 主机地址                                       |
| --port |            | 27017     | 主机端口                                       |
| -u     | --username |           | 用户名                                         |
| -p     | --password |           | 密码                                           |
| -d     | --db       |           | 数据库实例，这个名称可以与备份时候的名字不一样 |
| --drop |            |           | 备份前删除所有数据，请谨慎使用                 |
| --dir  |            |           | 指定备份的文件目录                             |

例：	mongorestore -h -h 127.0.0.1:27017 -d sqlmonit -u sqlmonit -p sqlmonit 

--dir C:\Users\wyWan\Desktop\sqlmonit 

### （2）mongoimport

mongoimport[options]

| 参数        | 其他用法     | 默认值    | 说明                                           |
| ----------- | ------------ | --------- | ---------------------------------------------- |
| -h          | --host       | 127.0.0.1 | 主机地址                                       |
| --port      |              | 27017     | 主机端口                                       |
| -u          | --username   |           | 用户名                                         |
| -p          | --password   |           | 密码                                           |
| -d          | --db         |           | 数据库实例，这个名称可以与备份时候的名字不一样 |
| -c          | --collection |           | 导入集合，导入到那个集合去                     |
| --file      |              |           | 要导入的文件                                   |
| --jsonArray |              |           | 导入json格式的文件                             |
|             |              |           |                                                |

