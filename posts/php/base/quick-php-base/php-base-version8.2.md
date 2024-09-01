---
title: php8.2新特性
date: 2023-02-02 08:00:00
description: PHP系列 - php8.2新特性
tags:
- PHP
nav: PHP系列
categories:
- PHP基础
keywords:
- PHP系列
- Php新版本、PHP8.2
image: post/design/colorhunt.jpg
---
## 移除动态属性
动态特性的一个很好的例子是动态属性。允许在任何对象上设置类中不存在的属性
```php
class User{}

$user = new User();
$user->email = 'info@spatie.be';
```

假设您在某处重命名此属性。然后你要找到所有使用这个属性的地方，因为它需要重命名

从PHP 8.2开始，动态属性被弃用。上面的示例现在将抛出弃用警告。当您实现魔法__get()或__set()方法时，获取和设置对象的动态属性仍然有效。
```php
#[AllowDynamicProperties]
class User{}

$user = new User();
$user->email = 'web@myfreax.com';
```

## 字符串插值弃用
目前 PHP 可通过以下方式在带有双引号 (") 和 heredoc 的字符串中插入变量
- 直接插入变量： “$foo”
- 在变量外添加花括号： “{$foo}”
- 在 $ 符号后面添加花括号： “${foo}” //从php8.2开始被弃用
- 定义可变的变量语法 ( “${expr}”，等同于 (string) ${expr}

```php
"Hello there, {$name}"
"Hello there, ${name}"// 被弃用 Using ${} in strings is deprecated.
```


参考
- https://www.myfreax.com/new-features-in-php-8-2-released/