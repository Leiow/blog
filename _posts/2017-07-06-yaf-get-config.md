---
layout : post
title : "Yaf 学习记录（2）"
subtitle : "Yaf 获取配置信息"
date : 2017-07-06 12:00:00
author : "Leiow"
header-img : "img/post-bg-install-yaf.jpg"
catalog : true
tags : 
    - 技术
    - PHP
    - Yaf
---

> Carry on


## 故事角色介绍

我创建了一个名字叫**yaf**的小人物，他的样貌是这样的

```
+ application
    - Bootstrap.php
    + controllers
        - Index.php
    + views
        + index
            - index.phtml
+ conf
    - application.ini
- index.php
```

## 故事的开始

Yaf手册看到了路由的部分，由于安装完之后，就一直没有着手写过什么代码，总感觉少点什么，并且路由对于每一个框架，应该都算是很重要的部分。所以就开始写了几行代码。可是真的是不动手不知道会有什么问题。明明按照手册的说明去写的代码，却一直得不到想要的结果。难道阴天会闹鬼吗？


## 嗯，就这么开始了
给yaf做了个小小的配置

```ini
[yaf]
;APPLICATION_PATH is the constant defined in index.php
application.directory=APPLICATION_PATH "/application/" 

;product section inherit from yaf section
[product:yaf]
foo=bar

[common]
routes.simple.type = 'simple'
routes.simple.controller = 'c'
routes.simple.module = 'm'
routes.simple.action = 'a'

```

入口文件是这样的：

```php
<?php
define('APPLICATION_PATH', dirname(__FILE__));

$app = new Yaf_Application(APPLICATION_PATH . '/conf/application.ini');
$app->bootstrap()->run();
```

安装laruence的手册上写的一样，引导文件的代码写成了这样：

```php
<?php
class Bootstrap extends Yaf_Bootstrap_Abstract {

    public function _initRoute(Yaf_Dispatcher $dispatcher) {
        $router = Yaf_Dispatcher::getInstance()->getRouter();
        $router->addConfig(Yaf_Registry::get('config')->routes);
    }
    
}
```

然而。。。

就这么遇上鬼了。。。

报错了。。。。。。。。。

```
Notice: Trying to get property of non-object in /usr/local/var/www/yaf/application/Bootstrap.php on line 9

Warning: Yaf_Router::addConfig(): Expect a Yaf_Config_Abstract instance or an array, null given in /usr/local/var/www/yaf/application/Bootstrap.php on line 9
```

为啥没有对象。。。

网上查资料，大家好像都是这样，都没问题吗？我就不明白了。。。没有哪写的不对吧？

可是就是不对

## 怂了

既然报错，我就换种方式吧

```php
class Bootstrap extends Yaf_Bootstrap_Abstract {

    public function _initRoute(Yaf_Dispatcher $dispatcher) {
        $config = new Yaf_Config_ini(APPLICATION_PATH . '/conf/application.ini', 'common');
        
        $router = Yaf_Dispatcher::getInstance()->getRouter();
        var_dump($router->addConfig($config->routes));
    }
}
```

看看结果：

```
object(Yaf_Router)#5 (2) { ["_routes":protected]=> array(2) { ["_default"]=> object(Yaf_Route_Static)#6 (0) { } ["simple"]=> object(Yaf_Route_Simple)#11 (3) { ["controller":protected]=> string(1) "c" ["module":protected]=> string(1) "m" ["action":protected]=> string(1) "a" } } ["_current":protected]=> NULL }
```

这回好了，终于拿到routes中的simple的参数了。

## 求教

由于一脸懵逼，尝试了很多次，也查了一些资料，但是都没有解决。于是请教了下leader。leader说了两种解决方案：

### 其一

一般情况下，不会通过配置中的不同配置名称去获取配置，可以通过配置的节点进行配置的获取。

于是，我把yaf这个小怪物的配置改了[捂脸][捂脸][捂脸]

```ini
[yaf]
;APPLICATION_PATH is the constant defined in index.php
application.directory=APPLICATION_PATH "/application/" 
application.routes.simple.type = 'simple'
application.routes.simple.module = 'm'
application.routes.simple.controller = 'c'
application.routes.simple.action = 'a'

;product section inherit from yaf section
[product:yaf]
foo=bar
```

然后引导文件变成了这样：

```php
<?php
class Bootstrap extends Yaf_Bootstrap_Abstract {

    public function _initConfig(Yaf_Dispatcher $dispatcher) {
        Yaf_Registry::set('config', Yaf_Application::app()->getConfig());
    }
    public function _initRoute(Yaf_Dispatcher $dispatcher) {
        $routes_config = Yaf_Registry::get('config');
        var_dump($routes_config['application']['routes']);
        $router = Yaf_Dispatcher::getInstance()->getRouter();
        $router->addConfig($routes_config['application']['routes']);
        var_dump($router);
    }
}

```

来看下输出的内容：

```
object(Yaf_Config_Ini)#11 (2) { ["_config":protected]=> array(1) { ["simple"]=> array(4) { ["type"]=> string(6) "simple" ["module"]=> string(1) "m" ["controller"]=> string(1) "c" ["action"]=> string(1) "a" } } ["_readonly":protected]=> bool(true) } 
object(Yaf_Router)#5 (2) { ["_routes":protected]=> array(2) { ["_default"]=> object(Yaf_Route_Static)#6 (0) { } ["simple"]=> object(Yaf_Route_Simple)#11 (3) { ["controller":protected]=> string(1) "c" ["module":protected]=> string(1) "m" ["action":protected]=> string(1) "a" } } ["_current":protected]=> NULL }
```

不好看是吧。。。看这个

```
object(Yaf_Config_Ini)#11 (2) {
  ["_config":protected]=>
  array(1) {
    ["simple"]=>
    array(4) {
      ["type"]=>
      string(6) "simple"
      ["module"]=>
      string(1) "m"
      ["controller"]=>
      string(1) "c"
      ["action"]=>
      string(1) "a"
    }
  }
  ["_readonly":protected]=>
  bool(true)
}
object(Yaf_Router)#5 (2) {
  ["_routes":protected]=>
  array(2) {
    ["_default"]=>
    object(Yaf_Route_Static)#6 (0) {
    }
    ["simple"]=>
    object(Yaf_Route_Simple)#11 (3) {
      ["controller":protected]=>
      string(1) "c"
      ["module":protected]=>
      string(1) "m"
      ["action":protected]=>
      string(1) "a"
    }
  }
  ["_current":protected]=>
  NULL
}
```

看看，两个对象，这就是对了吧

这样，就可以通过Yaf_Registry::get()获取到所有的配置内容，由于返回的是一个数组对象，所以，可以通过key来获取到你想要的任何配置。然后就可以把自己的配置写到路由中。

bingo！！！

### 其二

通过对象的方式，进行路由的配置。

首先，Yaf提供了不同的路由协议，如下：

>Yaf_Route_Simple
Yaf_Route_Supervar
Yaf_Route_Static
Yaf_Route_Map
Yaf_Route_Rewrite
Yaf_Route_Regex

然后呢，嘿嘿😜，我就这么干了

```php
<?php
class Bootstrap extends Yaf_Bootstrap_Abstract {

    public function _initRoute(Yaf_Dispatcher $dispatcher) {
        $route = Yaf_Dispatcher::getInstance()->getRouter();
        $router_simple = new Yaf_Route_Simple('m', 'c', 'a');
        $route->addRoute('simple', $router_simple);
        var_dump($route);
    }
}
```

来看看输出是啥吧

```
object(Yaf_Router)#5 (2) {
  ["_routes":protected]=>
  array(2) {
    ["_default"]=>
    object(Yaf_Route_Static)#6 (0) {
    }
    ["simple"]=>
    object(Yaf_Route_Simple)#9 (3) {
      ["controller":protected]=>
      string(1) "c"
      ["module":protected]=>
      string(1) "m"
      ["action":protected]=>
      string(1) "a"
    }
  }
  ["_current":protected]=>
  NULL
}
```

不用说啥了，结果已经很明显了


### 尝试

再尝试下其他的协议，gogogo

#### Yaf_Route_Supervar

```php
<?php
class Bootstrap extends Yaf_Bootstrap_Abstract {

    public function _initRoute(Yaf_Dispatcher $dispatcher) {
        $route = Yaf_Dispatcher::getInstance()->getRouter();

        // 请求方式 index.php?yaf=/module/controller/action
        $router_supervar = new Yaf_Route_Supervar('yaf');
        $route->addRoute('supervar', $router_supervar);
        var_dump($route);
    }
}
```

输出：

```
object(Yaf_Router)#5 (2) {
  ["_routes":protected]=>
  array(5) {
    ["_default"]=>
    object(Yaf_Route_Static)#6 (0) {
    }
    ["supervar"]=>
    object(Yaf_Route_Supervar)#10 (1) {
      ["_var_name":protected]=>
      string(3) "yaf"
    }
  }
  ["_current":protected]=>
  NULL
}
```

#### Yaf_Route_Rewrite

```php
<?php
class Bootstrap extends Yaf_Bootstrap_Abstract {

    public function _initRoute(Yaf_Dispatcher $dispatcher) {
        $route = Yaf_Dispatcher::getInstance()->getRouter();

        // 请求方式 http://host/product/blabla
        $router_rewrite = new Yaf_Route_Rewrite(
            'product/:ident',
            [
                'controller' => 'products',
                'action' => 'soso'
            ]
        );
        $route->addRoute('rewrite', $router_rewrite);
        var_dump($route);
        //控制器里用:$this->getRequest()->getParam('ident');
    }
}
```

输出：

```
object(Yaf_Router)#5 (2) {
  ["_routes":protected]=>
  array(5) {
    ["_default"]=>
    object(Yaf_Route_Static)#6 (0) {
    }
    ["rewrite"]=>
    object(Yaf_Route_Rewrite)#11 (3) {
      ["_route":protected]=>
      string(14) "product/:ident"
      ["_default":protected]=>
      array(2) {
        ["controller"]=>
        string(8) "products"
        ["action"]=>
        string(4) "soso"
      }
      ["_verify":protected]=>
      NULL
    }
  }
  ["_current":protected]=>
  NULL
}
```

#### Yaf_Route_Regex

```php
<?php
class Bootstrap extends Yaf_Bootstrap_Abstract {

    /*public function _initConfig(Yaf_Dispatcher $dispatcher) {
        Yaf_Registry::set('config', Yaf_Application::app()->getConfig());
    }*/
    public function _initRoute(Yaf_Dispatcher $dispatcher) {
        $route = Yaf_Dispatcher::getInstance()->getRouter();

        // 请求方式 http://host/product/blabla
        $router_regex = new Yaf_Route_Regex(
            'product/([a-zA-Z0-9])',
            [
                'controller' => 'products',
                'action' => 'soso'
            ],
            [
                1 => 'ident'
            ]
        );
        $route->addRoute('regex', $router_regex);
        var_dump($route);
        //这样控制器里用:$this->getRequest()->getParam('ident');
    }
}
```

输出：

```
object(Yaf_Router)#5 (2) {
  ["_routes":protected]=>
  array(5) {
    ["_default"]=>
    object(Yaf_Route_Static)#6 (0) {
    }
    ["regex"]=>
    object(Yaf_Route_Regex)#12 (5) {
      ["_route":protected]=>
      string(21) "product/([a-zA-Z0-9])"
      ["_default":protected]=>
      array(2) {
        ["controller"]=>
        string(8) "products"
        ["action"]=>
        string(4) "soso"
      }
      ["_maps":protected]=>
      array(1) {
        [1]=>
        string(5) "ident"
      }
      ["_verify":protected]=>
      NULL
      ["_reverse":protected]=>
      NULL
    }
  }
  ["_current":protected]=>
  NULL
}
```

### PS

[laruence的手册]("http://www.laruence.com/manual/")有点老旧了，有些地方写的不对，比如[这里](http://www.laruence.com/manual/yaf.routes.static.html)，在介绍[命令行使用Yaf]("http://www.laruence.com/manual/yaf.incli.times.html")时候，以Yaf_Route_Simple为例。这个类的构造函数可以接受五个参数，而当五个参数都给了默认值的时候，路由的类中routed属性为1。可是目前最新版本的Yaf，这个类的构造函数，已经改为三个参数了（详见[PHP手册]("http://cn2.php.net/manual/zh/yaf-route-simple.construct.php")和[Github]("https://github.com/laruence/yaf/blob/master/routes/yaf_route_simple.c")）。另外文中还有部分语法错误。
个人并不是要喷什么，而是说，现在学习Yaf，laruence的手册，可以作为参考，带你慢慢入门。如果遇到问题，还是自己动手实现以下，或者参考PHP手册和Github上的说明和源码比较好。


***That's all***


