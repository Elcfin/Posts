---
title: MongoDB 学习笔记
categories: 杂记
tags: [MongoDB]
date: 2021-11-12 9:00:00
updated: 2022-10-12 9:00:00
---

### Windows

#### 基础安装、配置与连接

官网下载，选择合适的版本，zip 版解压后可直接使用

重命名为 mongodb 并更改位置（便于后续与路径相关的操作）

此时路径为 D:/mongodb

建立 D:/mongodb/data

建立 D:/mongodb/data/db

建立 D:/mongodb/data/log

此时可官网下载图形界面管理工具 mongoDB compass，安置在 D:/mongodb/mongodb-compass

建立 D:/mongodb/mongod.conf 配置文件

先简单配置一下

```shell
systemLog:
    destination: file
    path: d:\mongodb\data\log\mongod.log
storage:
    dbPath: d:\mongodb\data\db
```

进入 D:/mongodb/bin

打开命令行窗口，先执行

```shell
mongod -f d:/mongodb/mongod.conf
```

打开新窗口，再执行

```shell
mongo
```

**Bingo**

### Linux

#### 基础安装、配置与连接

官网下载，选择合适的版本，解压后可直接使用

![](http://post-image.oss-cn-hangzhou.aliyuncs.com/img/Linux 版本选择.png)

重命名为 mongodb 并更改位置（便于后续与路径相关的操作）

此时路径为 /usr/local/mongodb

建立 /usr/local/mongodb/data

建立 /usr/local/mongodb/data/db

建立 /usr/local/mongodb/data/log

建立 /usr/local/mongodb/mongod.conf 配置文件

先简单配置一下

```shell
systemLog:
    destination: file
    path: /usr/local/mongodb/data/log/mongod.log
storage:
    dbPath: /usr/local/mongodb/data/db
net: 
    port: 27017 
    bindIp: 192.168.189.128 # 一定要配，否则远程无法连接
```

查找 linux 服务器的ip，在终端输入 ifconfig

进入 /usr/local/mongodb/bin

打开终端，先执行

```shell
mongod -f /usr/local/mongodb/mongod.conf
```

打开新终端，再执行

```shell
mongo 192.168.189.128
```

在 Windows 浏览器中访问 [192.168.189.128:27017](http://192.168.189.128:27017/)

![](http://post-image.oss-cn-hangzhou.aliyuncs.com/img/Windows 连接成功.png)

**Bingo**

