---
title: Mysql各个版本变化
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

#### 5.6
- 索引下推
    ```
    name age联合索引
    select * from user where name like "%范" and age=20
    5.6之前版本
    根据最左原则、查找name为范所有数据，忽略age数据，根据name查找的主键ID,拿这id值一次次会标查询
    5.6后版本
    Innodb没有忽略age字段，在索引内部就判断age是否等于20，获取id,去主键索引回表查询全部数据，减少匹配数
    explain 下字段extr
    Using index condition
    ```
- GTID复制
```
每个GTID对应事务在每个实例上面最多执行一次，提高复制数据一致性
```
#### 5.7
- 支持json格式
#### 5.7.6
- GTID事务控制
    - 每个事务由唯一个gtid标识，当slave执行成功写入硬盘完成事务
    - 如果master突然宕机，则回滚事务，保证数据一致性
    - enforce_gtid_consistency = ON
    - gtid_mode = ON
    
#### 8
- 速度
    - 快于5.7两倍
    - 窗口函数，类似sum,但是不需要group by
    - 索引可以被隐藏和显示
    - 降序索引
    - 默认utf8mb4默认字符集
    - JSON，JSON_EXTRACT() JSON_ARRAYAGG() JSON_OBJECTAGG()
    - 对OpenSSL改进
    - 默认身份验证
    - SQL角色
    - 密码强度
    - 授权
- 功能
    - 查询缓存功能被废弃

#### 自增ID
- Innodb,自增ID保存到redo日志，重启可以会恢复
- mysql8之前，自增ID记录到内存中，重启或OPTIMIZE会使最大ID丢失
    
#### 参考
- https://blog.csdn.net/weixin_40449300/article/details/107804133