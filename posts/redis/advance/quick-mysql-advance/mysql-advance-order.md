---
title: order by id limit 1导致的故障
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

#### 故障现象

慢查询SQL

> select \* from order where uid=1 order by id asc limit

#### 故障分析

1、explain查看实际执行

> key走primary

2、optimizer\_trace查看优化器生成执行计划

    set optimizer_trace="enabled=on";
    select * from order where uid=1 order by id asc limit

    select * from information_schema.OPTIMIZER_TRACE;
    set optimizer_trace="enabled=off"

#### 故障原因

order by id asc基于id排序写法，优化器会认为排序是个非常昂贵操作，避免排序，并且认为limit n的n如果很小，使用全表扫描也可以执行排序

建议以下三种写法

> select \* from order force index(uid) where uid=1 order by id asc

> select \* from order where uid=1 order by(id+0) asc limit 1

> select \* from (select \* from order where uid=1) a order by a.id asc limit 1

参考

*   <https://zhuanlan.zhihu.com/p/428649963>


