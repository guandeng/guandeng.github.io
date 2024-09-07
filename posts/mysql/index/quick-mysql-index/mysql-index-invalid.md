---
title: Mysql索引失效
date: 2022-08-15
description: Mysql索引
tags:
- Mysql
nav: Mysql系列
categories:
- Mysql索引
keywords:
- Mysql索引
- Mysql索引
image: post/design/colorhunt.jpg
---



### &#x20;查询条件中的值的长度大于索引字段定义的长度

```sql
mysql> CREATE TABLE `table_a` (
  `id` int(11) NOT NULL,
  `b` varchar(10) DEFAULT NULL,
  `c` varchar(10) DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `b` (`b`)
) ENGINE=InnoDB;
```

```sql
select * from table_a where b='1234567890abcd';
```

查询字符串超过b定义varchar(10),引擎会截取前10个字符，查询出的结果后，mysql server会再次进行过滤，如果截取的字符匹配非常多数据，server层处理也非常慢



### &#x20;小结

*   对索引字段进行函数操作，会破坏索引值有序性，优化器放弃树搜索功能
*   explain除了关注是否使用索引外，还需要关注rows

参考

  - <https://mp.weixin.qq.com/s/lEx6iRRP3MbwJ82Xwp675w>