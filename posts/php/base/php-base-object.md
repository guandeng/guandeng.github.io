---
title: 对象赋值是否属于引用 - 基础
date: 2023-12-02 08:00:00
description: PHP系列 - 工厂模式
tags:
- PHP
nav: PHP系列
categories:
- 基础
keywords:
- PHP系列
- Php新版本、PHP8.3
image: post/design/colorhunt.jpg
---

## 对象赋值在PHP中到底是不是引用

先看个例子
```php
<?php

class SimpleClass
{
    public $var = null;
}
$instance = new SimpleClass();

$assigned = $instance;
$reference = &$instance;

$instance->var = '$assigned will have this value';

$instance = null; // $instance and $reference become null

var_dump($instance);// null
var_dump($reference);// null
var_dump($assigned);// object
```

首先，将PHP中的变量看成是一个一个的数据槽。这个数据槽可以保存一个基本类型（int、string、bool等）。创建引用时，这个槽里保存的是内存地址，或者说是指向引用对象的一个指针，引用没有拷贝操作，仅仅是将指针指向了原变量（参考数据结构）。创建普通赋值时，则是拷贝的基本类型。

而对象则与基本类型不同，它不能直接保存在数据槽中，而是将对象的“句柄”保存在了数据槽。这个句柄是指向对象特定实例的标识符。虽然句柄不是我们所能直观操作的类型，但它也属于基本类型。

当你获取一个包含对象句柄的变量，并将其分配给另一个变量时，另一个变量获取的是这个对象的句柄。（注意，不是引用！不是引用！不是引用！！）。通过句柄，两个变量都可以修改同一个对象。但是，这两个变量并没有直接关系，它们是两个独立的变量，其中一个变量修改为其他值时，并不会对另一个变量产生影响。只有该变量在修改对象内部的内容时，另一个变量因为持有相同的句柄，所以它的对象内容也会相应地发生改变。

#### 参考

[对象赋值在PHP中到底是不是引用？](https://mp.weixin.qq.com/s/wKIU83A7u1ENQF32jX5FSQ)

