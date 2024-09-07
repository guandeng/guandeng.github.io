---
title: Mysql数据类型
date: 2022-02-14
description: Mysql系列
tags:
- Mysql
nav: Mysql系列
categories:
- Mysql基础
keywords:
- Mysql系列
image: post/imgs/mysql查询流程.png
---

#### 整形
- tinyint:1byte
- smallint:2bytes
- mediumint:3bytes
- int:4bytes
- bingint:8bytes
- tinyint,smallint,mediumint,int,bigint分别使用8,16,24,32,64位存储空间
- int(11)没有实际意义，和int(10)没有多大区别，只是在宽度不同，设置宽度为了自动填充zerofill才提现
- 
#### 浮点数
- float：4bytes
- double:8bytes

#### 字符串
- char:定长，会删除字符串末尾空格,存储上线255【字节】，
- varchar:不定长，不会删除字符串末尾空格，最大存储65535【字节】

#### 时间和日期
- datetime:使用8字节存储空间，与时区无关
- timestamp:与时区有关，使用from_unixtime()转换为日期，unix_timestamp()将时间转为时间戳，空间使用率比datetime高
- 
#### 优化点
- 用整形存储ip
- 尽量选择更小的数据类型存储
- 避免使用null

#### varchar(5)和varchar(200)区别
两者存储`hello`开销一样，在内存临时表进行排序，磁盘临时表进行排序，空间大消耗内存多

#### count(*)和count（1）和count(列)区别
- count(*)：相当于行数，不会过滤掉null
- count(1),不会过滤掉null值
- count(列)：会过滤掉列字段为null，不统计null的行

- 我的结论：列为主键：count(列)比count(1)快，列不为主键count(1)比count(列)快
- 

#### in和exists()区别