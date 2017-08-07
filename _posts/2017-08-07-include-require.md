---
layout : post
title : "PHP 基础"
subtitle : "include require 系列"
date : 2017-08-07 09:43:00
author : "Leiow"
header-img : "img/post-bg-macbook.jpg"
catalog : true
tags : 
    - 技术
    - PHP
    - 基础
---

## `include` 和 `require` 介绍

`include` 和 `require` 都是 PHP 中引如文件的一种语言结构，通过它们，可以在当前 PHP 脚本中，引入外部的 PHP 文件。这个外部，可以指的是本地文件系统中的文件，也可以是远程服务器中的文件。但是，对于远程服务器的文件，不建议这么去引用，可以使用 `readfile()` 。

## 关于引入文件的如何搜索的问题

使用时，如果给定了引入文件的路径，无论是绝对路径还是相对路径，那么会直接从指定的路径中去获取文件，如果获取不到文件，则会报错（或者警告）。

如果没有指定文件路径，只是给出了文件名称，`include_path` 都会被完全忽略。例如一个文件以 `../` 开头，则解析器会在当前目录的父目录下寻找该文件。

## 区别

引入文件，是这一系列函数的共同的功能。在 PHP 手册中，它们的功能，也是基本一致的。那么区别在哪呢？

- 首先，在对引入文件错误的处理上，两者是不一样的。如果引入一个不存在的文件，那么 `include` 会发出一个 `E_WARNING` 级别的错误，对于这种错误，不会影响后续 PHP 程序的运行；而 `require` 则会发出 `E_ERROR` 级别的错误，这将导致整个 PHP 脚本结束运行。所以，对于引入文件对本次程序影响不大的前提下，使用 `include` 对文件进行引入，会比较好。

- 其次，它们在加载时机不同。`require` 在脚本执行时，就对文件进行加载，而 `include` 则在使用的时候，或者说，在程序执行到 `include` 所在行的时候，才会对文件进行引入。

- 然后，引入文件时的处理方式不一样。 `require` 对引入文件只处理一次，如果引入文件没有变化，那么之后的引入，将不再对该文件进行处理；而 `include` 则在每次引入的时候，都会对文件进行处理。所以，由于不再进行处理，那么在对同一个文件进行多次引入的情况下， `require` 的效率，会比 `include` 的要高。如下例：

```php
echo '-----  require start -----', PHP_EOL;
$start_time = microtime(true);
for ($i = 0; $i < 1000; $i ++) {
    require 'a.php';
}
$end_time = microtime(true);
echo 'running : ', $end_time - $start_time, PHP_EOL;

sleep(2);

echo '-----  include start  -----', PHP_EOL;
$start_time = microtime(true);
for ($i = 0; $i < 1000; $i ++) {
    include 'a.php';
}
$end_time = microtime(true);
echo 'running : ', $end_time - $start_time, PHP_EOL
```

这个例子中引入的 `a.php` 中，只有一行，就是 `$a = 'a';` ，也就是只有一个变量的赋值语句。然后通过计算 1000 次循环所需要的时间，进行比较两者的效率。上例的输出如下：

```
-----  require start -----
running : 0.022936820983887
-----  include start  -----
running : 0.030122041702271
```

可以看到，在执行 1000 次的情况下，`require` 会比 `include` 快了将近 0.01 秒。

- 最后，要注意的是，网上有很多帖子会说 `include` 是有条件包含，而 `require` 则是无条件包含。这个怎么理解呢？简单点说，就是 `require` 在任何情况下，都会对文件进行引入，而 `include` 只有在条件满足的情况下，才会引入文件。但是，事情貌似并不是这样。看下面的例子

```php
if (false) {
    require '1a.php';
}

if (false) {
    include '1a.php';
}
```

上例中，`1a.php` 文件并不存在，但是执行后，没有任何输出。该种说法应该是错误的。

## `_once` 

`include_once` 和 `require_once` 这一组，看名字应该能知道大概，也都是引入文件，但是在引入的时候，都会进行判断，如果已经引入，则不会再次引入。其他的功能，跟 `include` 和 `require` 相同。

## 对于返回值

网上有一种说法，是 `include` 有返回值，而 `require` 没有返回值。我不太理解这里所谓的返回值是什么意思。下面看一个例子：

`a.php` 文件内容

```php
echo 'Hello', PHP_EOL;
```

`include.php` 文件内容

```php
var_dump(require 'a.php');
var_dump(require 'a.php');
```

脚本执行后的打印信息如下：

```
Hello
int(1)
Hello
int(1)
```

将 `include.php` 中的 `require` 改为 `include` ，输出结果相同。

再来试一下 `_once`

修改 `include.php` 文件，将 `require` 改为 `require_once`。

输出结果如下：

```
Hello
int(1)
bool(true)
```

`include_once` 同样是这种输出。

另外，请注意 PHP 手册中，还有这样一句话：

>因为 include 是一个特殊的语言结构，其参数不需要括号。在比较其返回值时要注意。

不知道网上的说法，是不是依据此而来。但是个人觉得网上的这种说法还是错误的。

## 关于语言结构

在查找 `include` 和 `require` 的区别的时候，有说法是，这两个，为 PHP 的语言结构。那么什么是语言结构？

PHP 的语言结构，可以理解为 PHP 的关键词，是语法的一部分，它不可以被用户定义或者添加到语言扩展或者库中；它可以有也可以没有变量和返回值。 

我们怎么判断是否是语言结构，或者换句话说，怎么判断是不是函数呢？

可以通过 `function_exists()` 这个函数，去判断。那么我们来试一下。

```php
var_dump(function_exists('str_replace'));
var_dump(function_exists('include'));
var_dump(function_exists('include_once'));
var_dump(function_exists('require'));
var_dump(function_exists('require_once'));
```

执行后的输出如下：

```
bool(true)
bool(false)
bool(false)
bool(false)
bool(false)
```

第一个，是 PHP 自带的用于字符串替换的函数，所以返回为 `true` 。后面的都为 `false`。那么我们是不是可以认为这一些列都是语言结构呢？

不！！！！

请注意，在 PHP 的手册中，介绍函数 `function_exists()` 的时候，有这样的一句话：

>Note:
对于语法结构的判断，例如 include_once 和 echo 将会返回 FALSE 。

所以，一定注意，`include_once` 不是语言结构，它就是函数！

另外，在手册中，介绍 `include` 的时候，有这样的一句话：

> ... 如果最后仍未找到文件则 include 结构会发出一条警告；...

这里，用到了 `结构` ，我个人的理解，从这里，也可以说明 `include` 为语言结构。而在 `include_once` 的介绍中，并没有出现这样的字眼。

所以，再次说明：

**`include` 和 `require` 为语言结构，而 `include_once` 和 `require_once` 为函数。**

## 语言结构与函数的区别

1. 语言结构比对应功能的函数快 
2. 语言结构在错误处理上比较鲁棒，由于是语言关键词，所以不具备再处理的环节 
3. 语言结构不能在配置项(php.ini)中禁用，函数则可以。 
4. 语言结构不能被用做回调函数

**备注：**

`php.ini` 中怎样禁用函数？ 
`php.ini` 中查找 `disable_functions = `
在等于后添加函数名，多个函数名用 `,` 分割 
比如：
`disable_functions = 
exec,passthru,popen,proc_open,shell_exec,system,chgrp,chmod,chown`

