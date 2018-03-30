---
layout : post
title : "CURL HTTPS"
date : 2018-03-30 12:00:00
author : "Leiow"
header-img : "img/post-bg-coffee.jpeg"
catalog : true
tags : 
    - 技术
    - 基础
---

## 前记

在开发的过程中，很多时候，需要通过 `CURL` 发送 HTTP 请求，完成数据操作。

在比较老版本的 Linux 系统中，或者默认安装的 `CURL`，可能是不支持 HTTPS 协议的。当发起请求的时候，会返回错误信息，类似于：

> Protocol https not supported or disabled in libcurl
>
> Unsupported protocol

这对于现在四处都是 HTTPS 的网站来讲，将无法满足业务的需求。

## 查看 CURL 信息

想要知道自己安装的 CURL 支持什么协议，可以通过下面指令获取（注意，是大写的 `V`）:

> curl -V

输出信息如下：

```
curl 7.19.7 (x86_64-redhat-linux-gnu) libcurl/7.19.7 NSS/3.21 Basic ECC zlib/1.2.3 libidn/1.18 libssh2/1.4.2
Protocols: tftp ftp telnet dict ldap ldaps http file https ftps scp sftp 
Features: GSS-Negotiate IDN IPv6 Largefile NTLM SSL libz
```

如上面的信息，在 `Protocols` 中打印出来当前 CURL 所支持的协议。

可以看到，目前这台服务器中是不支持 HTTPS 协议的。

## 安装

如果想让 CURL 支持 HTTPS 协议，首先必须安装 openSSL 静态库。我选择直接通过 yum 进行安装。

> yum install openssl openssl-devel

安装完成后，继续安装 CURL。我选择安装的是截止当前的最新版本。

> wget https://curl.haxx.se/download/curl-7.59.0.tar.gz

之后解压，进入目录

> tar -zxvf curl-7.59.0.tar.gz
>
> cd curl-7.59.0
>
> ./configure --prefix=/usr/bin/
>
> make && make install

完成安装后，再次查看 CURL 的信息

> curl -V

如果安装成功，输出的 `Protocols` 将会包含 https。

## php-curl

如果在不支持 HTTPS 协议的情况下安装 PHP，其中的 curl 扩展也会不支持 HTTPS 协议。

解决办法，就是如上所述的安装步骤，完成 CURL 的更新，之后重新编译 PHP。

在编译的条件中，应该增加 `--with-curl=/usr/bin/`，指定 CURL 的绝对路径。然后编译安装。

之后可以通过 `phpinfo()` 来查看 PHP 中的 curl 扩展是否支持 HTTPS。
