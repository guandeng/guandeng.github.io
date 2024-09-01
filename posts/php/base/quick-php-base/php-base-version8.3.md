---
title: php8.3新特性
date: 2023-12-02 08:00:00
description: PHP系列 - 工厂模式
tags:
- PHP
nav: PHP系列
categories:
- PHP基础
keywords:
- PHP系列
- Php新版本、PHP8.3
image: post/design/colorhunt.jpg
---

PHP 的 gc_status() 函数返回垃圾回收器(GC)的统计信息，比如 GC 是否在运行、GC 是否被保护以及 缓冲区(buffer)的大小。在调试长时运行PHP应用检测和优化内存使用时，这些信息很有用。

> var_dump(gc_status());

当前，gc_status 返回有4个key的数组：

| Field     | Type    | Description                    |
| --------- | ------- | ------------------------------ |
| runs      | Integer | GC 已运行的次数                |
| collected | Integer | 已收集的对象个数               |
| threshold | Integer | 缓冲区中用以触发 GC 的 root 数 |
| roots     | Integer | 当前缓冲区中 root 的数量       |

**在 PHP 8.3 中，gc_status 函数将额外返回4个字段**

| Field       | Type    | Description                                                                         |
| ----------- | ------- | ----------------------------------------------------------------------------------- |
| running     | Integer | 如果 GC 在运行中，则返回 true；否则返回 false                                       |
| protected   | Boolean | 如果 GC 被保护，且禁止添加 root，则返回 true; 否则返回 false                        |
| full        | Boolean | 如果 GC 犯冲区的大小超过 GC_MAX_BUF_SIZE(当前设为0x40000000，即= 1024³) 则返回 true |
| buffer_size | Integer | 当前 GC 的缓冲区大小。                                                              |

#### 向下兼容性影响
PHP 8.3 中，gc_status 函数在返回的数组中返回了额外的字段。函数的签名不变、返回类型也不变，只引入了4个新字段。

鉴于该函数返回的是 PHP 内部 GC 数据，因此无法使用用户空间的 PHP 函数获得这些额外信息。因此在旧版的 PHP 中没办法实现这一更新。

#### 参考

[PHP 8.3: gc_status()](https://php.watch/versions/8.3/gc_status-additional-information)

