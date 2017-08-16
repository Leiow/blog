---
layout : post
title : "ECMAScript 6"
subtitle : "Generator"
date : 2017-08-16 10:09:00
author : "Leiow"
header-img : "img/post-bg-coffee.jpeg"
catalog : true
tags : 
    - 技术
    - 前端
    - 基础
    - ES6
---

**注：代码运行的浏览器为 Chrome 60.0.3112.101**

## 基本概念

Generator 函数是 ES 6 提供的一种异步编程解决方案。从语法上，可以将这个函数理解为一个状态机，里面封装了多个内部状态。

执行 Generator 函数会返回一个遍历器对象，也就是说，Generator 函数除了是一个状态机，还是一个遍历器对象生成函数。返回的遍历器，可以依次遍历 Generator 函数内部的每一个状态。

Generator 函数是一个普通函数，但是有两个特征：
1. function 关键字和函数名称之间有一个 `*`;
2. 函数体内部使用 `yield` 表达式，定义不同的内部状态。

## `next()` 函数的使用

```javascript
function* firstGenerator() {
  yield 'hello';
  yield 'world';
  return 'over';
}
let first = firstGenerator();
console.log(first.next());
console.log(first.next());
console.log(first.next());
console.log(first.next());
```

上例输出结果：

```
{value: "hello", done: false}
{value: "world", done: false}
{value: "over", done: true}
```

可以看到，上例中，只输出了三个内容，第四次调用 `.next()` 的时候，没有输出信息。

前两次运行，分别执行了两个 `yield` 语句，拿到了相应的返回值，`done` 属性为 true ；第三次执行到 `return`，获取返回值，该函数结束，`done` 属性为 false，代表遍历结束。之后再执行，将不会有任何输出。

`yield` 表达式，在使用 `next()` 函数的时候，相当于一个暂停标志。程序运行到 `yield` 的时候，将暂停后续代码的执行，只有调用了 `next()` 函数，内部指针指向该语句的时候，才会执行。

```javascript
function* firstGenerator()
{
  yield 'hello';
  console.log('first inner tag');
  yield 'world';
  console.log('second inner tag');
  return 'over';
}
var first = firstGenerator();
console.log(first.next());
console.log('first outer tag');
console.log(first.next());
console.log('second outer tag');
console.log(first.next());
```

上例的输出结果如下：

```
{value: "hello", done: false}
first outer tag
first inner tag
{value: "world", done: false}
second outer tag
second inner tag
{value: "over", done: true}
```

`next()` 函数在使用的时候，可以传入一个参数，该参数用于本次执行时的参数（或者说上一次 `yield` 位置的参数），先看例子：

```javascript
function* secondGenerator(x)
{
  var y = 2 * (yield (x + 1));
  console.log('inner x : ' + x + '; y : ' + y);
  var z = yield(y / 2);
  console.log('inner x : ' + x + '; y : ' + y + '; z : ' + z);
  return x + y + z;
}

var second = secondGenerator(5);
console.log(second.next(5));
console.log(second.next(5));
console.log(second.next(5));
```

返回结果：

```
{value: 6, done: false}
inner x : 5; y : 10
{value: 5, done: false}
inner x : 5; y : 10; z : 5
{value: 20, done: true}
```

1. 在第一次使用 `next()` 函数的时候，遇到第一个 `yield (x + 1)` 的时候返回结果，也就是返回了 `x + 1` 的结果，为 6，同时记录了 x 的值为 5；
2. 第二次使用 `next()` 函数的时候，传入参数为 5，从上一次调用暂停的位置，也就是 `yield (x + 1)` 的位置开始，这个 `yield` 表达式被替换为 5，所以得到的 y 的结果为 10 ，遇到第二个 `yield (y / 2)` 返回结果，也就是 5，同时记录了 y 的结果为 10；
3. 第三次使用 `next()` 函数的时候，传入参数同样为 5，同样从上一次暂停位置开始，则变量 z 的值为 5，通过前两次对 x 和 y 的值的记录，那么最后返回三个变量相加的结果，就是 5 + 10 + 5 = 20。

**注意：由于 `next()` 方法代表上一次 `yield` 表达式的返回值，所以在第一次使用 `next()` 的时候，传参是无效的。V8 引擎自动忽略第一次使用 `next()` 传入的参数，只有从第二次 `next()` 使用时，参数才是有效的。从语义上讲，第一次是用来启动遍历器对象的，所以不用传参**

## 使用 `for ... of ...` 遍历

使用 `for ... of ...` 循环进行遍历，则不需要再使用 `next()`。

```javascript
function* thirdGenerator()
{
  yield 1;
  yield 2;
  yield 3;
  return 4;
}
var third = thirdGenerator();
for (let val of third) {
  console.log(val);
}
```

上例输出结果：

```
1
2
3
```

首先，使用 `for ... of ...` 循环的方式，实现了对 Generator 函数的遍历；
其次，函数中 `return` 的结果并没有输出。这是因为在最后执行 `return` 获取的返回值中，`done` 的属性为 `true` ，此时，循环结束且不包含返回对象，所以最后 `return` 的结果就没有输出。

## return 方法

Generator 提供了一个 `return` 方法，用于终结遍历 Generator 函数。

```javascript
function* fourGenerator()
{
  yield 1;
  yield 2;
  yield 3;
}
var four = fourGenerator();
console.log(four.next());
console.log(four.return('over'));
console.log(four.next());
```

上例输出结果：

```
{value: 1, done: false}
{value: "over", done: true}
{value: undefined, done: true}
```

可以看到，如果执行了 `return()` 函数，那么迭代就终止了，并返回了该函数中传入的参数。
如果 `return()` 函数没有传入参数，那么将会返回一个 `undefined`。

有一个特例，如果在 Generator 函数中，存在 `try {...} finally {...}` 代码块，那么 `return()` 将推迟到 `finally` 代码块执行完成后再执行。

```javascript
function* fiveGenerator()
{
  yield 1;
  try {
    yield 2;
  } finally {
    yield 3;
    yield 4;
  }
  yield 5;
}
```

上例输出结果：

```
{value: 1, done: false}
{value: 2, done: false}
{value: 3, done: false}
{value: 4, done: false}
{value: "over", done: true}
{value: undefined, done: true}
...
```


