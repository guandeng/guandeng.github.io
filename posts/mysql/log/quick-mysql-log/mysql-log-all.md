---
title: Mysql日志大全
date: 2023-02-02
description: 存储系列
tags:
- Mysql
nav: 存储系列
categories:
- Mysql日志
keywords:
- 存储系列
image: post/imgs/mysql查询流程.png
---

## 一、redo log(重做日志)
通过配置`innodb_log_files_in_group`配置文件数量和`innodb_log_file_size`每个日志文件大小
- 通过重做日志和InnoDB存储引擎日志缓冲实现
- 通过循环写方式记录，写到结尾，会回到开头循环写日志
- 事务中操作，写入存储引擎日志缓冲，事务提交前，缓冲的日志提前刷新磁盘持久化（日志先行），事务提交后，buffer pool映射数据才刷新到磁盘
- binlog日志和redo log必须保持一致

##### 为什么还有redo log
- redo log大小固定，日志采用循环写，旧日志数据会被覆盖，无法数据回滚/恢复操作
- redo log只有innodb实现，其他引擎层不兼容

##### 宕机或崩溃恢复
- 系统启动，为redo log分配一块连续存储空间，以顺序方式记录redo log,通过顺序IO改善性能
- 系统重启，根据redo log记录日志，把数据库恢复到崩溃前状态，未完成事务，可以继续提交，也可以回滚，

## 二、undo log回滚日志
#### 场景
多版本并发控制MVCC下，记录操作数据前的值
- 除了记录redo log 还会记录Undo log,undo log记录数据在每个操作前状态，单个事务回滚，只会回滚当前事务操作，并不会影响其他事务操作

## binlog SQL日志


#### mysql多少种日志
- 1、慢查询日志
- 2、查询日志
- 3、二进制日志
- 4、中继日志:slave用来恢复
- 5、错误日志
- 6、事务日志：重做redo和回滚日志undo


### 分布式事务
- 1、InnoDB支持原生事务
    - 应用程序
    - 资源管理器
    - 事务管理器
- 2、消息队列实现分布式事务一致性
