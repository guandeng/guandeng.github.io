---
title: Mysql8.0主从搭建
date: 2022-05-10
description: 存储系列
tags:
- Mysql
nav: 存储系列
categories:
- Mysql基础
keywords:
- 存储系列
image: post/imgs/mysql查询流程.png
---

> 文章只为记录本地搭建主从环境测试过程

#### mysql下载地址
- https://dev.mysql.com/downloads/mysql/

主服务器上创建用户，并给权限

```
create user 'slave1'@'%' identified by '123456';
grant replication slave on *.* to 'slave1'@'%';
flush privileges;
```




重启主服务器
```
mysql.server restart
```

查看主服务器状态
```
show master status;
```
```
mysql> show master status;
+---------------+----------+--------------+------------------+ 
| File | Position | Binlog_Do_DB | Binlog_Ignore_DB | 
+---------------+----------+--------------+------------------+ 
| binlog.000001 | 1304 | db1 | | 
+---------------+----------+--------------+------------------+ 
1 row in set (0.00 sec)
```

从机配置与启动
```
mysql_install_db
```

修改 /etc/my.cnf配置
```
[mysqld]
server-id=1
sql-mode="STRICT_TRANS_TABLES,NO_ENGINE_SUBSTITUTION"
log-slow-queries=/usr/local/mysql/data/fan-2-slow.log
long_query_time=1

binlog-do-db = liang
log-bin=/var/lib/mysql/binlog
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock

[mysqld_multi]
mysqld=/usr/local/bin/mysqld_safe
mysqladmin=/usr/local/bin/mysqladmin
log=/usr/local/var/www/mysql/mysqld_multi.log

[mysqld3307]
mysqld=mysqld
mysqladmin=mysqladmin
datadir=/usr/local/var/www/mysql/3307/data
port=3307
server_id=3307
socket=/usr/local/var/www/mysql/mysql_3307.sock
log-error=/usr/local/var/www/mysql/3307/error_3307.log
```
启动从机
> ./mysqld --datadir=/usr/local/var/www/mysql/3307/data --initialize --initialize-insecure

> ./mysqld --datadir=/usr/local/var/www/mysql/3308/data --initialize --initialize-insecure

 > ./mysqld_multi --defaults-extra-file=/etc/cluster.conf --user=mysql start 3307
 
 验证是否成功
 ```
mysqld_multi --defaults-extra-file=/etc/cluster.conf report
 
mysqld --defaults-file=/etc/my.conf --initialize  --basedir=/usr/local/var/www/mysql  --datadir=/usr/local/var/www/mysql/3307/data
 ```