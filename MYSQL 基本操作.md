---
title: MYSQL 基本操作
categories: 杂记
tags: [mysql]
date: 2022-10-11 20:41:00
updated: 2022-10-11 20:41:00
---

### 命令行操作

**启动数据库**

```shell
net start mysql80 # 本机数据库名为 mysql80，需要在管理员模式
```

**关闭数据库**

```shell
net stop mysql80
```

**登录数据库**

```shell
mysql -uroot -p123456 # -u账号 -p密码 
```

登录成功，然后就可以操作数据库啦

### 数据库操作

**退出数据库登录**

```mysql
quit;
```

**查看当前用户全部数据库**

```mysql
show databases;
```

**新建数据库**

```mysql
create database 数据库名;
```

**选择数据库**

```mysql
use 数据库名;
```

**删除数据库**

```mysql
drop database 数据库名;
```

**查看当前数据库全部表**

```mysql
show tables;
```

**查看表结构**

```mysql
desc 表名;
```

