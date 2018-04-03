---
layout : post
title : "Laravel 中使用 Vue"
date : 2018-04-03 15:55:00
author : "Leiow"
header-img : "img/post-bg-coffee.jpeg"
catalog : true
tags : 
    - 技术
    - 基础
    - Laravel
    - Vue
---

- 进入项目根目录，使用 `npm install` 安装。

- 修改路由

路径：`routes/web.php`

增加或修改已生成的路由，此处使用默认的路由进行后续操作。j

```php
Route::get('/', function () {
    return view('welcome');
});
```

- 修改 blade 模板

修改 `resources/views/welcome.blade.php` 文件中，在 `body` 中增加一个 id 为 app 的 div，在其中使用app.js 中注册的组件，需要注意的就是要添加 crsf-Token 的验证 meta 标签，和引入 app.js 文件，这个 js 文件也可以去根目录中的 webpack.mix.js 文集中修改。

```html
<body>
  <div id="app">
  </div>
</body>
<script src="/js/app.js"></script>
```

需要注意：
1. 这里的 js 目录指向为 `resources/assets/js/`；
2. `script` 标签不能使用 `<script src="xxx" />` 形式。

- 使用 `npm install vue-router --save` 安装。

- 在 `resources/assets/js` 目录下创建 `router.js`，`App.vue`。

- 在 `App.vue` 中添加如下代码

```html
<template>
  <div>
      <router-view></router-view>
  </div>
</template>

<script>
export default {
  
}
</script>
```

- 在 `router.js` 文件中添加如下内容

```js
import Vue from 'vue';
import VueRouter from 'vue-router';

Vue.use(VueRouter);

export default new VueRouter({
  routes: [
    {
      name: 'test',
      path: '/',
      component: resolve => {
        require(['./components/TestComponent'], resolve);
      },
    }
  ],
});
```

- 在 `resources/assets/js/components/` 目录下创建 `TestComponent.vue`，写入如下内容：

```html
<template>
  <h1>Test Component</h1>
</template>
```

- 修改 `app.js`

```js

/**
 * First we will load all of this project's JavaScript dependencies which
 * includes Vue and other libraries. It is a great starting point when
 * building robust, powerful web applications using Vue and Laravel.
 */

require('./bootstrap');

window.Vue = require('vue');

import App from './App.vue';
import router from './router';

/**
 * Next, we will create a fresh Vue application instance and attach it to
 * the page. Then, you may begin adding components to this application
 * or customize the JavaScript scaffolding to fit your unique needs.
 */

Vue.component('example', require('./components/ExampleComponent.vue'));

const app = new Vue({
    el: '#app',
    router,
    render: h => h(App),
});
```

- 运行 `npm run dev` 或者 `npm run watch` 编译完成后，访问即可。
