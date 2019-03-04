---
layout : post
title : "iView 的 DatePicker 组件的使用"
date : 2019-03-04 14:15:00
author : "Leiow"
header-img : "img/post-bg-coffee.jpeg"
catalog : true
tags : 
    - 技术
    - 基础
    - iView
    - Vue
---

使用 `iView` 的 `DatePicker` 组件的时候，需要进行时间的获取，并保存。

但是绑定的时候，存储的时间总是与期望不符，会相差 8 小时，所以推断是时区的问题。

解决方法：

组件提供了一个事件方法 `on-change`，通过传入的参数，进行时区的本地化。

template: 

```html
<Table ...>
  ...
  <DatePicker
    type="datetime"
    placeholder="选择结束时间"
    v-model="item.expected_end_time"
    @on-change="(datetime) => { dateChange(datetime, index) }">
  </DatePicker>
</Table>
```

methods:

这里使用了 `dayjs` 进行了日期的格式化

```javascript
methods: {
  ...,
  dateChange(date, index) {
    // index 为列表的索引
    this.form_items[index].date = dayjs(date).format('YYYY-MM-DD HH:mm:ss');
  }
},
...,
```
