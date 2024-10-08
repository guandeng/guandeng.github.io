---
title: Mysql索引基础问答
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

#### 什么是索引
- 索引是帮助Mysql高效获取数据的数据结构，本质是一种数据结构，类似于字典，书籍目录
- 索引本身也是很大，内存不够存储，以文件形式存储在磁盘上
- 索引也是一张表：保存索引和主键字段，并执行实体表积累了，也需要占用内存
- 索引可以提高查询效率，但是insert、update、delete速度就没用那么理想，要调整更新带来的键值前后变化索引信息

#### 索引的优缺点
- 优点
    1、提高查询效率，降低磁盘IO
    2、降低数据排序的成本，降低CPU的消耗，索引之所以查的快，因为先将数据排序好
- 缺点
    1、索引本身是表，需要占用内存和硬盘空间
    2、对表进行insert、delete操作时，需要同时更新索引，否则指向物理数据可能不对

#### 为什么mysql用B+tree索引，不用b-tree
b+tree和b-tree不同之处
- 1、非叶子节点只存储键值信息和指针
- 2、所有叶子节点都有一个链指针
- 3、数据记录都存放在叶子节点
-![企业微信截图_20220602102530.png](https://note.youdao.com/yws/res/60294/WEBRESOURCEdb8dd255cae506e15d98f173a721fec9)
- B+tree有两个头指针，一个指向根节点，一个指向关键字最小叶子节点
使用B+tree主要原因
- B+树非叶子节点不存储数据，每一层存储索引数量增加，意味相同高度，B+树存储数量比B树多，可以减少磁盘IO
- B+树在查询效率比B树高，B+树所有存储叶子节点使用双向链表进行关联，查询只需要两个节点进行遍历，而B树需要遍历所有节点
- 


#### 为什么不用hash
- 1、hash底层是hash表，存在存在hash冲突
- 2、hash使用key-value结构存储，数据存储关系没有任何顺序，无法进行区间查询，需要等值查询
- 3、B+tree一种多路平衡查询，节点天然有序（左节点小于父节点，父节点小于右节点），查询不需要全表查询
- hash不支持联合索引最左匹配

#### B+tree和hash索引区别
    - hash索引是根据hash函数获取键值，B+tree是从根节点往下查找到符合条件的叶子节点上的键值，有可能回表查询数据
    - hash索引更快，无法范围查询
    - hash索引不支持排序
    - hash索引不支持模糊查询，最左前缀匹配也无法使用

#### 什么是最左前缀原则

#### 什么是索引下推
- 非索引下推是查询数据的过程是，服务层调用引擎层，只根where第一个条件去返回所有数据到服务层，再根据第二个，第三个条件去过滤数据
- 索引下推则是根据where所有条件到引擎层获取符合条件数据，减少存储引擎层查询基础表的次数，减少服务层从引擎层传输的数据，减少IO,提高效率
参考：https://blog.csdn.net/sinat_29774479/article/details/103470244

#### 如何使sql有效复合索引

#### sql优化步骤

#### 使用mysql索引原则
- 最左原则
- 避免回表
- 索引下推
- 覆盖索引

#### mysql 组合索引结构是什么

为什么要用B+树：https://mp.weixin.qq.com/s?__biz=MzIxMzk3Mjg5MQ==&mid=2247483916&idx=1&sn=bfc33b53f8176e6f4d7e64c087ad36a4&chksm=97afe0f8a0d869eeaa14d8b26eca9d6fa09f9fda4557b40cb22ebe75851aa4dfb67d822233d9&scene=0&subscene=90&sessionid=1539434820&ascene=7&devicetype=andro

#### 什么时候不用索引
- 频繁更新的字段不用建索引
- 大量重复字段的不用建索引
- 数据量很少的不用建索引