---
title: Mysql常用命令
date: 2022-06-14
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
#### 事务
```
查看当前会话隔离级别
SELECT @@tx_isolation;

查看系统隔离级别
select @@global.tx_isolation;

修改隔离级别(读已提交)
set session transaction isolation level read committed;

查看是否开启间隙锁
 show variables like 'innodb_locks_unsafe_for_binlog';
```
#### 索引
```
前缀索引
完整列索引选择性
select count(distinct saler) / count(*) from gj_store_delivers

查找合适前缀长度
select count(distinct left(saler,1))/count(*) as sel1, count(distinct left(saler,2))/count(*) as sel2, count(distinct left(saler,3))/count(*) as sel3, count(distinct left(saler,4))/count(*) as sel4 from gj_store_delivers;

设置前缀
alter table gj_store_delivers add index saler_index(saler(2));
```

#### 日志
```
手动刷新日志，新建binlog日志记录文件
flush logs

通过mysqlbinlog读取日志内容并通过管道传给mysql命令，-v表示执行此mysql命令)
/usr/bin/mysqlbinlog  --stop-position=435 --database=hello  /var/lib/mysql/mysql-bin.000006 | /usr/bin/mysql -uroot -p密码 -v hello

查看binlog是否开启
show variables like "%log_bin%"

查看binlog模式
show variables like 'binlog_format%';

```


#### 配置查询
```
查看数据库读写情况
show global status where Variable_name in('com_select','com_insert','com_delete','com_update')
```