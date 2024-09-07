---
title: Mysql 参数调优
date: 2021-08-14
description: 存储系列
tags:
- Mysql
nav: 存储系列
categories:
- Mysql进阶
keywords:
- 存储系列
image: post/design/colorhunt.jpg
---

# &#x20;脏页控制策略&#x20;

```sql
1、innodb_io_capacity
	作用：刷新脏页策略
	合理设置：建议设置成磁盘的 IOPS，通过 fio 工具测试
2、innodb_flush_neighbors
	作用：控制数据写入磁盘时临近页刷新行为
	合理设置：ssd 硬盘建议设置为 0
```

# 事务日志&#x20;

```sql
1、innodb_log_file_size
	作用：日志文件大小
	合理建议：
2、sync_binlog
	作用：控制事务提交时，将 binlog 日志写进磁盘策略
	合理设置：不建议设置 为 0，大于 1 表示累计多个事务合并写入磁盘，较大值平衡性能和持久性
3、innodb_flush_log_at_trx_commit
	作用：控制事务日志刷新策略
	合理设置：默认 1，事务提交后立即写日志，0不进行提交，2、设置缓冲区，通过延迟写入和缓冲区提高性能
4、redo log buffer 占用的空间即将达到 占 innodb_log_buffer_size一半的时候， 一 后台线程会主动写盘（只 write，没有 fsync)，并行的事务提交的时候，顺带将这个事务的 另 redo log buffer持久化到磁持盘
	
```

### Buffer 相关

```
1、show engine innodb status
	作用：查看buffer pool 命中率
	合理值：99%以上
2、innodb_buffer_pool_size
	作用：控制缓存池大小
	
```

### 现在出现了性能瓶颈，而且瓶颈在 现 IO上，可以通过哪些方法来提升性能呢

*   &#x20;1\. 设置 binlog\_group\_commit\_sync\_delay和 binlog\_group\_commit\_sync\_no\_delay\_count参 数，减少binlog的写盘次数。这个方法是基于“额外的故意等待”来实现的，因此可能会增加 语句的响应时间，但没有丢失数据的风险。&#x20;
*   2\. 将sync\_binlog 设置为大于1的值（比较常见是100\~1000）。这样做的风险是，主机掉电时 会丢binlog日志。&#x20;
*   3\. 将innodb\_flush\_log\_at\_trx\_commit设置为2。这样做的风险是，主机掉电的时候会丢数据。