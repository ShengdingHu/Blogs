---
layout: post
title: JavaScript难点
categories: JavaScript
description: Learning JS
keywords: callback, promise, 闭包
---

JavaScript为什么看起来这么复杂？为什么有回调，Promise，闭包诸如此类其他语言没有的东西？ 因为它是一门为了应对异步问题的语言。JavaScript多用于网络编程（的后端）,网络编程为了应对耗时的网络通信，不能按程序一行行顺序执行。试想，如果有一行发起一个网络请求，耗时1s，但这一行并不影响下几行的执行，那么难道要等1s才执行下一行？另一种情况是，10行都发起网络请求，各耗时1s，难道要用10s一行一行地执行？因此我们需要的是在发起网络请求的时候同时往下执行。这种异步性正是JS的独特性。可以说用Python写的东西，C都能写，无非是多一点，但是JS轻松处理的的东西，其他语言要实现就十分困难。

## 回调函数

回调函数是将一个函数作为参数传进另一个函数。类似于C语言中的传函数指针。这种特性在异步编程中尤为重要。

同步编程中，C语言的函数指针完全可以用其他方式实现。

比如

```
void recall(int x){
    printf(x);
}

void func(int x,void* recall){
    x=x*3;
    recall(x);
}

func(3) // 9

// <====> 完全等价于

int func(int x){
    x=x*3;
    return x
}

int y = 0
y = func(3)
recall(y) //9
```

但是如果 func本身是一个异步操作，比如他在云端，可能需要10s才能访问到这个函数。第二种实现就有问题了，因为func这个网络请求提交上去以后，还没有被更新成9， recall就输出了，因此输出为0 。而用第一种实现就没有问题，因为recall是在func里面的，所以recall要被执行一定是func被执行（请求到了）之后。

对于如何写回调函数，w3c等等网站有很好的的介绍

## 闭包

#### var that = this

我在小程序开发中最开始遇到的最大的困难，是如何在A函数的回调函数B中给A外面的一个json赋值。如果直接用this.setData 会报错 setData is not a function等等

问题出在this 指代这个回调函数B，没有理由认为会获得A外面的函数。解决这个问题很简单

在A外面写 

```javascript
var that = this
```

这时候在B里面虽然 this变了，但是that 没有变，还是外面那个变量。



## Promise

Promise 则是一种更好的解决异步问题的方案。用通俗的话说，是将一个语句A变成一个关于A的承诺： A执行完后，再去做B。

promise的一般形式就是如下，我们把 f 叫做 p对应的函数

```javascript
var f = function (resolve,reject){
   ///xxx
}
var p = new Promise(f)
```

但是到真正灵活运用的层面这种理解还不够。我们根据几行代码来理解

```javascript
var promise1 = new Promise(function(resolve, reject) {
  setTimeout(function() {
    resolve('foo');
  }, 300);
});

var f = function(value) {
  console.log(value);
} 

promise1.then(f)；
// 等价于 promise1.then(res=>f(res))；

console.log(promise1);
```

输出：

> [object Promise]
> "foo"



这里表面上看，是将 f 当做一个函数参数（类似于函数指针）传进了promise1对应的函数中，然后执行这个函数就会经过 resolve这个函数，也就是f。但是如果将f单纯视作一个回调的话，之后有很多问题会无法理解。

一种更直观（但不是原理）的方式是：<font color="#dd00dd">**p的resolve 相当于return ， 将‘foo' 返回，这个返回值被then拿到，并传给then里面的函数**。</font>（其实本质上以上两种叙述描述的过程是完全一样的，但是采用第二种的话对后续理解更有帮助）（ps: resolve不具有return那样终止函数的效果）

#### Promise.all

> 引用MDN 教程： The **Promise.all()** method returns a single [`Promise`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) that fulfills when all of the promises passed as an iterable have been fulfilled or when the iterable contains no promises. It rejects with the reason of the first promise that rejects.
>
> It is typically used after having started multiple asynchronous tasks to run concurrently and having created promises for their results, so that one can wait for all the tasks being finished.

<font color="#dd00dd"> 返回值这种理解在遇到Promise.all 的时候优势更明显。</font>看下面这个例子

```javascript
var promise1 = Promise.resolve(3);
var promise2 = 42;
var promise3 = new Promise(function(resolve, reject) {
  setTimeout(resolve, 100, 'foo');
});

var f = function(value) {
  console.log(value);
} 
Promise.all([promise1, promise2, promise3]).then(f);
// expected output: Array [3, 42, "foo"]

```

如果将resolve视为回调，那么 then中的f是传给这个复合的promise的。可是复合promise里面在什么地方调用了f呢，是遍历每一个子promise吗？如果是的话，当我们改变一下 f，也就是

```javascript
var promise1 = Promise.resolve(3);
var promise2 = 42;
var promise3 = new Promise(function(resolve, reject) {
  setTimeout(resolve, 100, 'foo');
});

var f=function(values) {
  console.log(values+'d');
}
Promise.all([promise1, promise2, promise3]).then(f);
```

输出的应该是 

> "3d,42d,food"

但是实际输出

>"3,42,food"  //注释:"3,42,food"= [3,42,'foo']+'d'

说明明显<font color="#dd00dd">不是遍历每一个子promise，执行回调</font>。

但是采用“返回值”的看法：

每个字promise有一个返回值，分别是3,42,'fool'  复合Promise的返回值是这些子promise返回值的列表，也就是[3,42,'foo'] ，这个promise会将这个列表传给then中的函数，因此输出 [3,42,"food"]  。



**有了promise这个工具，以及promise.all ，就可以让多个异步操作同时进行，如果需要还可以再它们都结束了再进行其他操作。**





## JS 与 微信小程序

写这个部分是因为，最近在用JS开发微信小程序，遇到了一些小程序特有的坑，在此记录：

1. Promise.allSettled 不支持
2. 云函数对回调的支持不如对Promise，有时候在云函数中回调会报莫名其妙的错，但是改成Promise之后就正常了。



#### 链接

<ul>

<a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise">Promise（MDN教程）</a>



