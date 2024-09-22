---
title: Mysql分页limit很慢
date: 2023-07-18
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

#### 故障现象
突然客服反馈，后台查询订单很慢，需要优化。问题出在我们常见数据列表，操作分页查询后，轮询到最后几页，查询很慢。语句如下：
```
select * from orders where create_time>'*****' order by id desc limit 1000000,20
```
一看执行时间，接近60秒。还好是客服自己后台，要是发生在会员前台后果不堪设想。

分页是一个很普通的功能，接下来就进入问题排查和优化
#### 原因分析
数据库查询中，当使用 LIMIT x, y 分页查询时，如果 x 值越大，查询速度可能会变慢。这主要是因为数据库需要扫描和跳过 x 条记录才能返回 y 条结果。随着 x 的增加，需要扫描和跳过的记录数也增加，从而导致性能下降。
> 例如 limit 1000000,10 需要扫描 1000010 行数据，然后丢掉前面的 1000000 行记录，所以查询速度就会很慢。

#### 解决方案

##### 深度分页优化建议
使用自增的ID作为分页的依据，而不是使用LIMIT x, y，前提需要保证ID是连续。
```
# 查询指定 ID 范围的数据
SELECT * FROM orders WHERE id > 100000 AND id <= 100010 ORDER BY id
# 也可以通过记录上次查询结果的最后一条记录的ID进行下一页的查询：
SELECT * FROM t_order WHERE id > 100000 LIMIT 10
```

##### 子查询
我们先查询出 limit 第一个参数对应的主键值，再根据这个主键值再去过滤并 limit，这样效率会更快一些。

阿里巴巴《Java 开发手册》中也有对应的描述：
```
7.【推荐】利用延迟关联或者子查询优化超多分页场景。
说明:MVSQL并不是跳过 offset 行，而是取 offset+N 行，然后返回放弃前 offset 行，返回 N 行，那当 ofset 特别大的时候，效率就非常的低下，要么控制返回的总页数，要么对超过特定阈值的页数进行 SQL 改写。正例:先快速定位需要获取的id 段，然后再关联:SELECT t1.* FROM 表1 as t1 , (select id from表1 where 条件 LIMIT 100000 ,20) as t2 where t1.id = t2.id
```
根据建议改写
```
select t1.* from ordes as t1, (select id from orders where create_time>'*****' limit 1000000,20) as t2 where t1.id = t2.id
```
子查询的结果会产生一张新表，会影响性能，应该尽量避免大量使用子查询。并且，这种方法只适用于 ID 是正序的。在复杂分页场景，往往需要通过过滤条件，筛选到符合条件的 ID，此时的 ID 是离散且不连续的。

##### 延迟关联
延迟关联的优化思路，跟子查询的优化思路其实是一样的：都是把条件转移到主键索引树，减少回表的次数。不同点是，延迟关联使用了 INNER JOIN（内连接） 包含子查询。
```
SELECT t1.* FROM t_order t1
INNER JOIN (SELECT id FROM t_order limit 1000000, 10) t2
ON t1.id = t2.id;
```
除了使用 INNER JOIN 之外，还可以使用逗号连接子查询。
```
SELECT t1.* FROM t_order t1,
(SELECT id FROM t_order limit 1000000, 10) t2
WHERE t1.id = t2.id;
```
覆盖索引
```
# 如果只需要查询 id, code, type 这三列，可建立 code 和 type 的覆盖索引
SELECT id, code, type FROM t_order
ORDER BY code
LIMIT 1000000, 10;
```
不过，当查询的结果集占表的总行数的很大一部分时，可能就不会走索引了，自动转换为全表扫描。当然了，也可以通过 FORCE INDEX 来强制查询优化器走索引，但这种提升效果一般不明显。






参考
- https://javaguide.cn/high-performance/deep-pagination-optimization.html#%E5%AD%90%E6%9F%A5%E8%AF%A2
- 聊聊如何解决 MySQL 深分页问题 - 捡田螺的小男孩 https://juejin.cn/post/7012016858379321358