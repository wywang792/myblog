---
title: fastDFS安装使用.md
date: 2020-11-13 14:23:04
categories:
  - 环境搭建
tags:
  - fastDFS
abbrlink: 
cover:
---



# 一、FastDFS在RedHat7.5中的安装

## 1、使用root用户进行安装

### (1)、下载相关安装包

```shell
# FastDFS下载路径：
https://github.com/happyfish100

# 如果需要nginx提供服务
# nginx下载路径：
http://nginx.org/en/download.html
```

![image-20201112231516924](assets/fastDFS安装使用/image-20201112231516924.png)

```
fastdfs：FastDFS本体包
libfastcommon：FastDFS分离出的一些共用函数包
fastdfs-nginx-module：FastDF和nginx的关联模块
```

![image-20201112235609733](assets/fastDFS安装使用/image-20201112235609733.png)

### (2)、安装gcc

```shell
# 如果已经有gcc的话，就无需安装了
# 验证是否有gcc
gcc -v

# 有yum环境
yum install -y gcc gcc-c++
yum -y install libevent

# 没有yum环境的，使用rpm包进行安装
rmp -ivh ./*.rpm --force --nodeps
```

![image-20201113000626550](assets/fastDFS安装使用/image-20201113000626550.png)

### (3)、安装libfastcommon

```shell
# 安装路径 /root/fastdfs
mkdir -pv /root/fastdfs

# 在存放包的安装包的地方进行解压缩
tar -xvf libfastcommon-1.0.43.tar.gz -C /root/fastdfs

# 一定要加上 -C 参数 表示更换目录的意思

# 进入到安装目录
cd /root/fastdfs/libfastcommon-1.0.43

# 进行编译
./make.sh
# 进行安装
./make.sh install

# 以下为执行安装后的安装信息，可以看到一些存放目录，这里进行记录
mkdir -p /usr/lib64
mkdir -p /usr/lib
mkdir -p /usr/include/fastcommon
install -m 755 libfastcommon.so /usr/lib64
install -m 644 common_define.h hash.h chain.h logger.h base64.h shared_func.h pthread_func.h ini_file_reader.h _os_define.h sockopt.h sched_thread.h http_func.h md5.h local_ip_func.h avl_tree.h ioevent.h ioevent_loop.h fast_task_queue.h fast_timer.h process_ctrl.h fast_mblock.h connection_pool.h fast_mpool.h fast_allocator.h fast_buffer.h skiplist.h multi_skiplist.h flat_skiplist.h skiplist_common.h system_info.h fast_blocked_queue.h php7_ext_wrapper.h id_generator.h char_converter.h char_convert_loader.h common_blocked_queue.h multi_socket_client.h skiplist_set.h fc_list.h json_parser.h buffered_file_writer.h /usr/include/fastcommon
if [ ! -e /usr/lib/libfastcommon.so ]; then ln -s /usr/lib64/libfastcommon.so /usr/lib/libfastcommon.so; fi
```

### (4)、安装FastDFS

```shell
# 在存放包的安装包的地方进行解压缩
tar -zxvf fastdfs-6.06.tar.gz -C /root/fastdfs

# 进入到安装目录
cd /root/fastdfs/fastdfs-6.06

# 进行编译
./make.sh
# 进行安装
./make.sh install

# 以下为执行安装后的安装信息，可以看到一些存放目录，这里进行记录
mkdir -p /usr/bin
mkdir -p /etc/fdfs
cp -f fdfs_trackerd /usr/bin
if [ ! -f /etc/fdfs/tracker.conf.sample ]; then cp -f ../conf/tracker.conf /etc/fdfs/tracker.conf.sample; fi
if [ ! -f /etc/fdfs/storage_ids.conf.sample ]; then cp -f ../conf/storage_ids.conf /etc/fdfs/storage_ids.conf.sample; fi
mkdir -p /usr/bin
mkdir -p /etc/fdfs
cp -f fdfs_storaged  /usr/bin
if [ ! -f /etc/fdfs/storage.conf.sample ]; then cp -f ../conf/storage.conf /etc/fdfs/storage.conf.sample; fi
mkdir -p /usr/bin
mkdir -p /etc/fdfs
mkdir -p /usr/lib64
mkdir -p /usr/lib
cp -f fdfs_monitor fdfs_test fdfs_test1 fdfs_crc32 fdfs_upload_file fdfs_download_file fdfs_delete_file fdfs_file_info fdfs_appender_test fdfs_appender_test1 fdfs_append_file fdfs_upload_appender fdfs_regenerate_filename /usr/bin
if [ 0 -eq 1 ]; then cp -f libfdfsclient.a /usr/lib64; cp -f libfdfsclient.a /usr/lib/;fi
if [ 1 -eq 1 ]; then cp -f libfdfsclient.so /usr/lib64; cp -f libfdfsclient.so /usr/lib/;fi
mkdir -p /usr/include/fastdfs
cp -f ../common/fdfs_define.h ../common/fdfs_global.h ../common/mime_file_parser.h ../common/fdfs_http_shared.h ../tracker/tracker_types.h ../tracker/tracker_proto.h ../tracker/fdfs_shared_func.h ../tracker/fdfs_server_id_func.h ../storage/trunk_mgr/trunk_shared.h tracker_client.h storage_client.h storage_client1.h client_func.h client_global.h fdfs_client.h /usr/include/fastdfs
if [ ! -f /etc/fdfs/client.conf.sample ]; then cp -f ../conf/client.conf /etc/fdfs/client.conf.sample; fi
```

**/usr/bin 中包含了可执行文件：**

![image-20201113002752908](assets/fastDFS安装使用/image-20201113002752908.png)



**/etc/fdfs 中包含了配置文件：**

![image-20201113002826792](assets/fastDFS安装使用/image-20201113002826792.png)

```shell
# 将安装文件夹下的配置文件拷贝到 /etc/fdfs 目录下
cp /root/fastdfs/fastdfs-6.06/conf/* /etc/fdfs
```

![image-20201113003206195](assets/fastDFS安装使用/image-20201113003206195.png)

### (5)、配置并启动tracker服务

```shell
# 修改tracker的配置文件
vim /etc/fdfs/tracker.conf

# tracker存储data和log的跟路径，必须提前创建好
base_path=/root/fastdfs/tracker

# 默认端口22122
port=22122

# http端口，需要和nginx相同，默认8080
http.server_port=8080

# 启动tracker
/usr/bin/fdfs_trackerd /etc/fdfs/tracker.conf start

# 关闭tracker，不建议使用系统的 kill -9 强制关闭，会导致文件信息不同步
/usr/bin/fdfs_trackerd /etc/fdfs/tracker.conf stop

# 重启tracker
/usr/bin/fdfs_trackerd /etc/fdfs/tracker.conf restart

# 检查进程
ps -ef | grep tracker

# 检查端口
netstat -nultp | grep 22122

# 停止tracker
/usr/bin/stop.sh /etc/fdfs/tracker.conf
```

### (6)、配置并启动storage服务

```shell
# 修改storage的配置文件
vim /etc/fdfs/storage.conf

#默认组名，根据实际情况修改
group_name=group1

#storge默认23000，同一个组的storage端口号必须一致
port=23000 

# storage存储data和log的跟路径，必须提前创建好
base_path=/root/fastdfs/storage

# 存储路径个数，需要和store_path个数匹配
store_path_count=1

# 第一个存储路径，如果为空，则使用base_path
store_path0=/root/fastdfs/data 

# 配置tracker的ip和port
tracker_server=192.168.43.131:22122 

# 启动storage
/usr/bin/fdfs_storaged /etc/fdfs/storage.conf start

# 关闭storage，不建议使用系统的 kill -9 强制关闭，会导致文件信息不同步
/usr/bin/fdfs_storaged /etc/fdfs/storage.conf stop

# 重启storage
/usr/bin/fdfs_storaged /etc/fdfs/storage.conf restart

# 检查进程
ps -ef | grep storage

# 通过monitor查看storage是否成功绑定
/usr/bin/fdfs_monitor /etc/fdfs/storage.conf
```



### (7)、测试上传

```shell
# 修改storage的配置文件
vim /etc/fdfs/client.conf

# client存储data和log的跟路径，必须提前创建好
base_path=/root/fastdfs/client

# 配置tracker的ip和port
tracker_server=192.168.43.131:22122 

# 测试上传
/usr/bin/fdfs_test /etc/fdfs/client.conf upload /etc/fdfs/anti-steal.jpg
```



## 2、使用普通用户进行安装

### (1)、下载相关安装包

```shell
## FastDFS下载路径：
https://github.com/happyfish100

## 如果需要nginx提供服务
## nginx下载路径：
http://nginx.org/en/download.html
```

![image-20201112231516924](assets/fastDFS安装使用/image-20201112231516924.png)

```
fastdfs：FastDFS本体包
libfastcommon：FastDFS分离出的一些共用函数包
fastdfs-nginx-module：FastDF和nginx的关联模块
```

**目录层级示意**
/home/fastdfs/
├── fastdfs
└── soft
├────fastdfs-5.11.tar.gz
├────fastdfs-nginx-module-1.20.tar.gz
├────libfastcommon-1.0.39.tar.gz
└────nginx-1.14.2.tar.gz

**安装路径及文件示意**
/home/fastdfs/fastdfs/
└── fastdfs
└── usr
├── include
│  └── fastcommon
│  ├── avl_tree.h
│  ├── base64.h
│  ├── chain.h
│  ├── char_converter.h
│  ├── char_convert_loader.h
│  ├── common_blocked_queue.h
│  ├── common_define.h
│  ├── connection_pool.h
│  ├── fast_allocator.h
│  ├── fast_blocked_queue.h
│  ├── fast_buffer.h
│  ├── fast_mblock.h
│  ├── fast_mpool.h
│  ├── fast_task_queue.h
│  ├── fast_timer.h
│  ├── fc_list.h
│  ├── flat_skiplist.h
│  ├── hash.h
│  ├── http_func.h
│  ├── id_generator.h
│  ├── ini_file_reader.h
│  ├── ioevent.h
│  ├── ioevent_loop.h
│  ├── local_ip_func.h
│  ├── logger.h
│  ├── md5.h
│  ├── multi_skiplist.h
│  ├── multi_socket_client.h
│  ├── _os_define.h
│  ├── php7_ext_wrapper.h
│  ├── process_ctrl.h
│  ├── pthread_func.h
│  ├── sched_thread.h
│  ├── shared_func.h
│  ├── skiplist_common.h
│  ├── skiplist.h
│  ├── skiplist_set.h
│  ├── sockopt.h
│  └── system_info.h
├── lib
│  └── libfastcommon.so -> /home/fastdfs/opt/fastdfs/usr/lib64/libfastcommon.so
└── lib64
└── libfastcommon.so

### (2)、安装gcc

### (3)、安装libfastcommon

### (4)、安装FastDFS

### (5)、配置并启动tracker服务

### (6)、配置并启动storage服务

### (7)、测试上传

