---
title: php8.4新特性
date: 2024-08-01 
description: PHP系列 - php8.4
tags:
- PHP
nav: PHP系列
categories:
- PHP基础
keywords:
- PHP系列
- Php新版本、php8.4
image: post/design/colorhunt.jpg
---

## 新特性
1. 属性钩子：这是一个重大的变化，允许开发者在属性访问前后添加自定义逻辑

2. JIT（即时编译）改进：PHP 8.4 引入了新的JIT实现，这基于IR（中间表示）框架。
   
8.4之前禁用JIT
> opcache.jit_buffer_size=0

8.4之后
```
opcache.jit=disable
opcache.jit_buffer_size=64m
```

1. DOM扩展升级：PHP 8.4 的DOM扩展进行了重大升级，支持HTML5的解析和序列化。这意味着在处理HTML5特定标签或在JavaScript中嵌入HTML时将更加方便。

2. 弃用和移除的功能：

- PHP_ZTS 和 PHP_DEBUG 常量值类型从int改为bool。

- Implicitly nullable parameter declarations deprecated。

- Curl: CURLOPT_BINARYTRANSFER deprecated。

5.更好的链式调用
PHP 8.4支持在不使用额外括号的情况下进行链式调用，这有助于减少代码的冗余并提高可读性。

8.4之前
> $name = (new ReflectionClass($objectOrClass))->getShortName();

8.4之后
> $name = new ReflectionClass($objectOrClass)->getShortName();

6.隐式可空类型弃用
8.4之前
> function save(Book $book = null) {}
8.4之后
> function save(?Book $book = null) {}


参考
- https://www.8hi.com.cn/read-69925-1.html