---
layout : post
title : "最近总结"
subtitle : "Bootstrap相关"
date : 2017-07-28 11:27:00
author : "Leiow"
header-img : "img/post-bg-macbook.jpg"
catalog : true
tags : 
    - 技术
    - 前端
    - Bootstrap
---


## 导航和导航条

- 导航组件依赖同一个 `.nav` 类，状态也是共用的。可以通过改变修饰类修改样式；
- 在标签页上使用导航，需要 JavaScript 标签页插件。由于标签页需要控制内容区的展示，因此，你必须使用 标签页组件的 JavaScript 插件。另外还要添加 role 和 ARIA 属性。
- 在使用导航组件实现导航条功能，务必在 `<ul>` 的最外侧的逻辑父元素上添加 `role="navigation"` 属性，或者用一个 `<nav>` 元素包裹整个导航组件。不要将 `role` 属性添加到 `<ul>` 上。

使用 `Javascript` 实现导航的切换：

```javascript
$('#myTabs a').click(function (e) {
  e.preventDefault()
  $(this).tab('show')
})
```

使用 `data-toggle` ，实现导航的切换：

```html
<div>
  <!-- Nav tabs -->
  <ul class="nav nav-tabs" role="tablist">
    <li role="presentation" class="active"><a href="#home" aria-controls="home" role="tab" data-toggle="tab">Home</a></li>
    <li role="presentation"><a href="#profile" aria-controls="profile" role="tab" data-toggle="tab">Profile</a></li>
    <li role="presentation"><a href="#messages" aria-controls="messages" role="tab" data-toggle="tab">Messages</a></li>
    <li role="presentation"><a href="#settings" aria-controls="settings" role="tab" data-toggle="tab">Settings</a></li>
  </ul>

  <!-- Tab panes -->
  <div class="tab-content">
    <div role="tabpanel" class="tab-pane active" id="home">...</div>
    <div role="tabpanel" class="tab-pane" id="profile">...</div>
    <div role="tabpanel" class="tab-pane" id="messages">...</div>
    <div role="tabpanel" class="tab-pane" id="settings">...</div>
  </div>

</div>
```

导航样式

1. `标签页` ，需要使用 `class="nav nav-tabs"`；
2. `胶囊式标签页` ，使用`class="nav nav-pills"`，如果需要使用垂直方向堆叠的导航，需要添加 `.nav-stacked` 类；
3. `两端对齐的标签页`，需要添加 `.nav-justified`;
4. 导航中需要禁用的链接，使用 `.disabled` 类，即可实现链接为灰色且没有鼠标悬停的效果。

关于导航条，需要注意的是：
1. 导航条作为应用或网站导航页头的响应式基础组件。可在移动端折叠，可开可关。当页口宽度增加时逐渐变为水平展开式；
2. 该组件依赖 Javascript 插件，如果被禁用，且视口足够窄，致使导航条折叠起来，导航条将不能被打开，.navbar-collapse 内所包含的内容也将不可见；

下面这个例子，是修改官方文档中的默认导航条，完成了导航条和导航两个组件的合并。

```html
<nav class="navbar navbar-default">
  <div class="container-fluid">
    <!-- Brand and toggle get grouped for better mobile display -->
    <div class="navbar-header">
      <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#bs-example-navbar-collapse-1" aria-expanded="false">
        <span class="sr-only">Toggle navigation</span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
      </button>
      <a class="navbar-brand" href="#">Brand</a>
    </div>

    <!-- Collect the nav links, forms, and other content for toggling -->
    <div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">
      <ul class="nav navbar-nav">
      <!-- <ul class="nav nav-tabs"> -->
        <li role="presentation" class="active"><a href="#home" aria-controls="home" role="tab" data-toggle="tab">Home <span class="sr-only">(current)</span></a></li>
        <li role="presentation"><a href="#docs" aria-controls="docs" role="tab" data-toggle="tab">Docs</a></li>
        <li role="presentation"><a href="#download" aria-controls="download" role="tab" data-toggle="tab">Download</a></li>
      </ul>
    </div><!-- /.navbar-collapse -->
  </div><!-- /.container-fluid -->
</nav>

<div class="tab-content">
	<div role="tabpanel" class="tab-pane active" id="home"><h1>HOME</h1></div>
	<div role="tabpanel" class="tab-pane" id="docs"><h1>DOCS</h1></div>
	<div role="tabpanel" class="tab-pane" id="download"><h1>DOWNLOAD</h1></div>
</div>
```
## bootstrap 图片自适应，拉伸

在 Bootstrap 中，提供了三种对图片应用简单样式的 class ，分别是：
`img-rounded`， `img-circle`， `img-thumbnail`，可以实现的效果是圆角、圆形、添加内边距和内框。

还有一个 class 是 `img-responsive`，可以实现图片的响应式。

示例：

```html
<img src="..." alt="..." class="img-rounded">
<img src="..." alt="..." class="img-circle">
<img src="..." alt="..." class="img-thumbnail">
```


