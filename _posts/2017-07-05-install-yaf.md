---
layout : post
title : "Yaf 学习记录（1）"
subtitle : "Yaf 安装"
date : 2017-07-05 15:40:00
author : "Leiow"
header-img : "img/post-bg-install-yaf.jpg"
catalog : true
tags : 
    - 技术
    - PHP
    - Yaf
---

> This is my own way

# yaf安装

1.安装依赖库

需要的依赖库包括：

> - gcc
> - gcc-c++
> - make
> - automake
> - autoconf


2.下载package

> http://pecl.php.net/package/yaf

3.解压缩安装包并进入目录

> tar -zxvf yaf-3.0.5.tar yaf-3.0.5
> cd yaf-3.0.5

4.准备扩展库的安装环境

> /usr/local/bin/phpize

执行成功，会看到如下输出：
> Password:
Configuring for:
PHP Api Version:         20160303
Zend Module Api No:      20160303
Zend Extension Api No:   320160303

5.配置

> ./configure --with-php-config=/usr/local/bin/php

6.编译安装

> make && make install

安装成功后，会有如下输出：

> Installing shared extensions:     /usr/local/Cellar/php71/7.1.6_18/lib/php/extensions/no-debug-non-zts-20160303/

查看扩展目录下的文件列表：

> ls /usr/local/Cellar/php71/7.1.6_18/lib/php/extensions/no-debug-non-zts-20160303/

可以看到yaf.so扩展文件，说明编译完成

7.在php.ini中增加yaf.so扩展

> vim /usr/local/etc/php/7.1/php.ini
> extension=yaf.so

8.重启php-fpm

> php-fpm.restart

9.查看phpinfo，可以看到yaf模块，完成安装

## 安装过程中遇到的问题

- 没有安装autoconf

在执行/usr/local/bin/phpize时，遇到如下错误输出

> Configuring for:
PHP Api Version:         20160303
Zend Module Api No:      20160303
Zend Extension Api No:   320160303
Cannot find autoconf. Please check your autoconf installation and the
$PHP_AUTOCONF environment variable. Then, rerun this script.

解决方法：

> 使用brew安装autoconf

## phpize介绍

> phpize命令是用来准备 PHP 扩展库的编译环境的。
> 下面例子中，扩展库的源程序位于 extname 目录中：

```
cd extend
/usr/local/bin/phpize
./configure
make && make install
```
成功的创建extend.so并放置于PHP的扩展目录中。只有需要修改php.ini，添加extention=extend.so之后，就可以使用extend.so扩展了。

使用phpize命令时，需要依赖autoconf依赖库。需要提前安装。


