---
layout : post
title : "SNMP实践"
date : 2017-07-18 10:01:00
author : "Leiow"
header-img : "img/bg-geek-blackboard.jpeg"
catalog : true
tags : 
    - 技术
    - 协议
    - SNMP
---

## 创建用户

SNMPv3增加了加密的内容，比较之前两个版本，有了更高的安全性。

在安装完SNMP扩展和NET-SNMP服务后，需要创建一个用户，用于获取或设置相关设备的数据

```
sudo net-snmp-config --create-snmpv3-user -ro -a 12345678 -x 12345678 -X AES -A MD5 rouser
```

这里创建了一个名为 `rouser` 的用户，使用的密码是 `12345678` ，加密方式为 `MD5`。***注意：这里需要使用管理员权限，否则权限不足的话，无法将用户写入配置文件***

命令运行成功，将会在屏幕上显示如下输出：

```
adding the following line to /var/db/net-snmp/snmpd.conf:
   createUser rouser MD5 "12345678" DES 12345678
adding the following line to /usr/share/snmp/snmpd.conf:
   rouser rouser
```

补充一点，在创建用户的时候，需要停用snmp服务，指令如下：

```
sudo launchctl unload -w /System/Library/LaunchDeamons/org.net-snmp.snmpd.plist
```

相应的，启动snmp服务的指令，如下：

```
sudo launchctl load -w /System/Library/LaunchDeamons/org.net-snmp-snmpd.plist
```

创建完成后，将会在配置文件中，增加该用户。

## 使用snmp

使用刚刚创建的用户，获取本机所有设备信息，并写入到指定文件中，方便查看。

```
snmpwalk -v 3 -u rouser -l authPriv -a MD5 -A 12345678 -x DES -X 12345678 localhost > /usr/local/var/www/snmp/snmp3_walk.log
```
 
获取到的部分数据如下：

```
...
IF-MIB::ifNumber.0 = INTEGER: 13
IF-MIB::ifIndex.1 = INTEGER: 1
IF-MIB::ifIndex.2 = INTEGER: 2
IF-MIB::ifIndex.3 = INTEGER: 3
IF-MIB::ifIndex.4 = INTEGER: 4
IF-MIB::ifIndex.5 = INTEGER: 5
IF-MIB::ifIndex.6 = INTEGER: 6
IF-MIB::ifIndex.7 = INTEGER: 7
IF-MIB::ifIndex.8 = INTEGER: 8
IF-MIB::ifIndex.9 = INTEGER: 9
IF-MIB::ifIndex.10 = INTEGER: 10
IF-MIB::ifIndex.11 = INTEGER: 11
...
```

## 实践

安装php的snmp扩展，具体步骤略了。

由于之前使用snmpwalk可以获取到本机的信息，所以使用snmp3_walk函数进行数据获取

code

```php
<?php
$snmp_walk = snmp3_walk('localhost', , 'rouser', 'authPriv', 'MD5', '12345678', 'DES', '12345678', '.1');
foreach ($snmp_walk as $info) {
    var_dump($info);
}
```

然而，问题就来了。运行该脚本，会提示错误，如下：

```
PHP Warning:  snmp3_walk(): Fatal error: Timeout in /usr/local/var/www/snmp/test.php on line 11

Warning: snmp3_walk(): Fatal error: Timeout in /usr/local/var/www/snmp/test.php on line 11
bool(false)
```

嗯，超时了。。。

为什么会超时呢？不解。这个函数还有两个可选变量，一个是超时时间 `$timeout` 默认为 `1000000` ，单位为微秒；一个是重试次数 `$retries` 默认为 `5`。但是对这两个参数进行修改，依旧拿不到参数。

不知道问题出在哪。。。之后把传入的localhost参数修改为127.0.0.1，问题就解决了。可能是因为无法解析 localhost 吧。

## 问题记录

在使用 `snmpget()` 函数的时候，如果 hostname 写成 `localhost`，代码如下：

```php
<?php
var_dump(snmpget('localhost', 'public', 'IF-MIB::ifIndex.13'));
```

那么将会有如下的问题：

```
PHP Warning:  snmpget(): No response from udp6:[::1] in /usr/local/var/www/snmp/test.php on line 5

Warning: snmpget(): No response from udp6:[::1] in /usr/local/var/www/snmp/test.php on line 5
bool(false)
```

这个问题也是由于 houstname 引起的。修改为设备IP地址，就可以解决。

---

使用 `snmpget()` 的函数的时候，无法拿到指定的参数数据。但是改为 `snmp3_get()` 就能正常获取。不知道是不是因为我本地添加了v3版本的用户造成的。再找找相关资料看看有没有解释吧。

---

