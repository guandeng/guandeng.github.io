---
title: 缓存系统Redis入门详解
date: 2022-05-10
description: 存储系列
tags:
- Redis
nav: 存储系列
categories:
- Redis基础
keywords:
- 存储系列
image: post/imgs/mysql查询流程.png
---

### Redis特性
- 速度快
- 持久化
    - redis所有的数据保存在内存中，对数据的更新将异步地保存到磁盘上
- 多种数据结构
- 支持多种编程语言
- 功能丰富
    - 发布订阅
    - lua脚本
    - 事务
    - pipeline
- 简单：代码短小精悍
- 主从复制
- 高可用、分布式
    - redis-sentinel支持高可用
    - 分布式 redis-cluster支持分布式


### 为什么用Nosql

### redis安装（mac&linux）
```
$ wget http://download.redis.io/releases/redis-4.0.8.tar.gz
$ tar xzf redis-4.0.8.tar.gz
$ cd redis-4.0.8
$ make
```
### 查看进程命令
```
ps aux | grep redis

客户端查看
redis-cli -h 127.0.0.1 -p 6379 ping 
```

- 查看配置
> redis-server configPath

- 动态参数启动
> redis-server --port 6380

- redis客户端连接
> redis-cli -h 127.0.0.1 -p 6379

### 数据结构和内部编码
![](https://cdn.jsdelivr.net/gh/guandeng/cdn/img161c710c4cc06b0d~tplv-t2oaga2asx-watermark.awebp)

### 常用命令
#### string类型操作
赋值 set key value
> 127.0.0.1:6379> set test 123

取值 get key
```
127.0.0.1:6379> get test
"123"
```
取值并赋值 getset key value
```
127.0.0.1:6379> getset test 321
"123"
127.0.0.1:6379> get test
"321"
```
设置获取多个键值 mset key value [key value...] mget key [key...]
```
127.0.0.1:6379> mset k1 v1 k2 v2 k3 v3
OK
127.0.0.1:6379> mget k1 k2
1) "v1"
2) "v2"

```
删除 del key
```
127.0.0.1:6379> del test
(integer) 1
```

#### Hash 散列类型
一次只设置一个字段值 语法：hset key field value
```
127.0.0.1:6379> hset user username zhangsan
(integer) 1
```
一次设置多个字段值 语法：hmset key field value [field value...]
```
127.0.0.1:6379> hmset user age 20 username lisi
OK
```
当字段不存在时赋值，类似hset,区别在于如果字段存在，该命令不执行任何操作。 语法：hsetnx key field value
```
127.0.0.1:6379> hsetnx user age 30
(integer) 0
```
一次获取一个字段值 语法：hget key field
```
127.0.0.1:6379> hget user username
"lisi"
```
一次可以获取多个字段值 语法：hmget key field [field...]
```
127.0.0.1:6379> hmget user age username
1) "20"
2) "lisi"
```
获取所有字段值 语法：hgetall key
```
127.0.0.1:6379> hgetall user
1) "username"
2) "lisi"
3) "age"
4) "20"
```

### List 类型
向列表左边增加元素 语法：lpush key value [value...]
```
127.0.0.1:6379> lpush list:1 1 2 3
(integer) 3
```

向列表右边增加元素 语法：rpush key value [value...]
```
127.0.0.1:6379> rpush list:1 4 5 6
(integer) 6
```

lrange key start stop(索引从0开始。索引可以是负数，如：“-1”代表最后边的一个元素)
```
127.0.0.1:6379> lrange list:1 0 2
1) "3"
2) "2"
3) "1"
127.0.0.1:6379> lrange list:1 0 -1
1) "3"
2) "2"
3) "1"
4) "4"
5) "5"
6) "6"
```

### Set 类型
集合类型：无序、不可重复 列表类型：有序、可重复

增加/删除元素 语法：sadd key member [member...]
```
127.0.0.1:6379> sadd set a b c
(integer) 3
127.0.0.1:6379> sadd set a
(integer) 0
```
语法：srem key member [member...]
```
127.0.0.1:6379> srem set c
(integer) 1
```
获得集合中的所有元素 语法：smembers key
```
127.0.0.1:6379> smembers set
1) "b"
2) "a"
```

### Redis集群
- Redis集群实现了对Redis的水平扩容，即启动N个redis节点，将整个数据库分布存储在这N个节点中，每个节点存储总数据的1/N
- Redis集群通过分区(partition)来提供一定程度的可用性(availability):即使集群中有一部分节点失效或者无法进行通讯，集群也可以继续处理命令请求