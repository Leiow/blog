---
layout : post
title : "Vue中的v-bind，v-model的区别"
date : 2017-07-07 13:50:00
author : "Leiow"
header-img : "img/post-bg-vue.jpg"
catalog : true
tags : 
    - 技术
    - 前端
    - JS
    - Vue
---

## 前记

今天在跟大佬学习Vue的时候，发现一个问题，就是Vue中的绑定数据的时候，会使用到v-bind和v-model。那么问题来了，这两个东西看似一样的东西，为啥要同时出现呢？

## 预备

首先了解下：

> Vue.js 使用了基于 HTML 的模板语法，允许开发者声明式地将 DOM 绑定至底层 Vue 实例的数据。所有 Vue.js 的模板都是合法的 HTML ，所以能被遵循规范的浏览器和 HTML 解析器解析。

> 在底层的实现上， Vue 将模板编译成虚拟 DOM 渲染函数。结合响应系统，在应用状态改变时， Vue 能够智能地计算出重新渲染组件的最小代价并应用到 DOM 操作上。

Vue采用DOM模板，也就是说它的模板可以直接当DOM节点运行。

绑定数据的方式有三种，一种是插值的方式，也就是通过双花括号进行数据的绑定；一种是通过v-bind；还有一种是通过v-model。

### 存在即合理

插值的方式进行数据绑定，在这里就不讨论了。

那么v-bind和v-model呢？

官方的解释是这样的：

> 动态地绑定一个或多个特性，或一个组件 prop 到表达式。

> 在绑定 class 或 style 特性时，支持其它类型的值，如数组或对象。可以通过下面的教程链接查看详情。

> 在绑定 prop 时，prop 必须在子组件中声明。可以用修饰符指定不同的绑定类型。

> 没有参数时，可以绑定到一个包含键值对的对象。注意此时 class 和 style 绑定不支持数组和对象。

也就是说v-bind是一个单向的绑定，根据绑定参数的变化而变化。

而v-model呢？

官方这么说：

> 在表单控件或者组件上创建双向绑定。

好了，那么字面上的解释，就比较明显了，一个单向，一个双向。一般情况下，v-model会用在`input`，`textarea`，`select`等这些表单元素中。因为其双向绑定的原因，使得绑定的元素可以被修改。

一般情况下，没必要混用v-bind和v-model。但当实际需要时，v-model建立的双向绑定对输入型元素input, textarea, select等具有优先权，会强制实行双向绑定。

## 语法糖

官方说v-model是v-bind和v-on的语法糖。可是想想，好像有点解释不通。那官方这种说法的依据或者说道理是什么呢？

其实，这个语法糖的意思，应该指的是对于input等表单元素而言的。比如一个input标签，可以写成这样：

```html
<input type="text" v-model='content' />
```

```js
new Vue({
    el : "#app",
    data : {
        content : ""
    }
});
```

双向绑定content，那么无论是在input中输入content或者直接修改content的值，那么都会有变化。

再来看看语法糖的意思吧。

这个input现在改造成这样：

```html
<input type="text" v-bind:value='content' v-on:input='something' />
```

```js
new Vue({
    el : "#app",
    data : {
        content : ""
    },
    methods : {
        something : function(event) {
            this.content = event.target.value
        }
    }
});
```

好了，这样就应该比较明显了吧。我们必须要将标签的value属性和事件进行绑定后，就可以实现v-model的功能了。所以语法糖的含义也就明白了吧。

## 后记

总结一下吧，其实v-bind和v-model的区别可以大致概括如下：

1. v-bind是数据和属性绑定，没有双向绑定的效果，可以使用在任何有效的元素上；
2. v-model是双向绑定的，基本上就用在表单相关的元素上；
3. v-bind和v-model同时出现的时候，v-model的优先级要相对高一些。


