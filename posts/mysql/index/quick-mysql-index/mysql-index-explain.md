---
title: Mysql索引
date: 2022-08-15
description: Mysql索引
tags:
- Mysql
nav: 存储系列
categories:
- Mysql索引
keywords:
- Mysql索引
- Mysql索引
image: post/design/colorhunt.jpg
---

#### explain的作用
查看执行计划

- select_type
    - DERIVED：from中包含子查询

### Extra
using index：覆盖索引

using where:使用索引情况下，需要回表查询所需数据

using index condition：使用索引，需要回表

using index & using where：查找使用了索引，但是需要的数据都在索引列中能找到，所以不需要回表查询数据

==Backward index scan== ：order by查询，使用反向扫描


### show profiles
查看执行情况
```
- 1 show profile
mysql> show profiles;

+----------+------------+---------------------------------------------------------------------------------------------------------+
| Query_ID | Duration   | Query                                                                                                   |
+----------+------------+---------------------------------------------------------------------------------------------------------+
|        1 | 10.62811400 | SELECT id,titile,published_at from spider_record where is_analyze=0 ORDER BY create_time desc LIMIT  10 |
|        2 | 10.78871825 | SELECT id,titile,published_at from spider_record where is_analyze=0 ORDER BY create_time desc LIMIT  10 |
|        3 | 0.05494200 | SHOW FULL TABLES WHERE Table_type != 'VIEW'                                                             |
|        4 | 0.01013250 | SHOW TABLE STATUS                                                                                       |
|        5 | 0.00034200 | SET profiling = 1                                                                                       |
|        6 | 10.65613175 | SELECT id,titile,published_at from spider_record where is_analyze=0 ORDER BY create_time desc LIMIT  10 |
+----------+------------+---------------------------------------------------------------------------------------------------------+
6 rows in set (0.07 sec)

- 2 show profile for query 6
+----------------------+----------+
| Status               | Duration |
+----------------------+----------+
| starting             | 0.000215 |
| checking permissions | 0.000017 |
| Opening tables       | 0.000085 |
| init                 | 0.000013 |
| System lock          | 0.000020 |
| optimizing           | 0.000020 |
| statistics           | 0.000049 |
| preparing            | 0.000034 |
| Sorting result       | 0.000010 |
| executing            | 0.000007 |
| Sending data         | 10.655393 |
| end                  | 0.000036 |
| query end            | 0.000030 |
| closing tables       | 0.000023 |
| freeing items        | 0.000151 |
| cleaning up          | 0.000033 |
+----------------------+----------+
16 rows in set (0.07 sec)
```
分析原因：  Sending data 耗时

- order by desc慢排查 https://juejin.cn/post/6844903874839445517