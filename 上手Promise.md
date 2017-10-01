---
title: 上手Promise
date: 2017-09-10 19:42:27
tags: Promise
categories: Javascript
---
# 前言
在JavaScript的世界中，所有代码都是单线程执行的。

由于这个“缺陷”，导致JavaScript的所有网络操作，浏览器事件，都必须是异步执行。异步执行可以用回调函数来实现。常见的例子如AJAX，就是典型的异步操作：
```javascript
request.onreadystatechange = function () {
    if (request.readyState === 4) {
        if (request.status === 200) {
            return success(request.responseText);
        } else {
            return fail(request.status);
        }
    }
}
```
把回调函数success(request.responseText)和fail(request.status)写到一个AJAX操作里很正常，但是不好看，而且不利于代码复用。所以，为了使代码更优雅。出现了这样的写法：
```javascript
var ajax = ajaxGet('http://...');
ajax.ifSuccess(success)
    .ifFail(fail);
```
这种链式写法的好处，不单单是优雅。它使得执行代码和处理结果的代码清晰的分离了。

<!-- more -->

# Promise基础介绍
Promise是异步编程的一种解决方案，比传统的解决方案——回调函数和事件——更合理和更强大。
## 常见用法
```javascript
var promise = new Promise(function(resolve, reject) {
    // ... some code
    if ( /* 异步操作成功 */ ) {
        resolve(value);
    } else {
        reject(error);
    }
});

promise.then(function(value) {
    // success
}, function(error) {
    // failure
});
```
再来张图：

![](/images/promise_status_.png)

看图说话：
1. Promise构造方法接受一个方法作为参数，该方法传入两个参数，resolve和reject。这两个参数都是函数。
2. resolve用来将Promise对象的状态置为成功，并将异步操作结果value作为参数传给成功回调函数。
3. reject用来将Promise对象的状态置为失败，并将异步操作错误error作为参数传给失败回调函数。
4. then方法绑定两个回调函数，第一个用来处理Promise成功状态，第二个用来处理Promise失败状态。


## Promise状态
因为Promise是用来异步操作的，所以它他有三种状态：
- Pending （执行中）
- Fulfilled (已完成)
- Rejectd （失败）
后两者对应着处理结果。

状态间的具体关系：

![](/images/promise-status)

如上图所示，Promise对象有两个特点：
1. 对象状态只由异步操作结果决定。resolve方法会使Promise对象由pendding状态变为fulfilled状态；reject方法或者异常会使得Promise对象由pendding状态变为rejected状态。Promise状态变化只有上图这两条路径。

2. 对象状态一旦改变，任何时候都能得到这个结果。即状态一旦进入fulfilled或者rejected，promise便不再出现状态变化，同时我们再添加回调会立即得到结果。这点跟事件不一样，事件是发生后再绑定监听，就监听不到了。

# 串行执行
通常要串行执行异步任务们，不用Promise需要写一层一层的嵌套代码。有了Promise，我们只需要简单地写：
```JavaScript
job1.then(job2).then(job3).catch(handleError);
```
可以这样写的原因是then返回的是promise的实例。这种写法也叫级联。

一个详细的例子：
```JavaScript
function log(s) {
    console.log(s);
}

// 0.5秒后返回input*input的计算结果:
function multiply(input) {
    return new Promise(function (resolve, reject) {
        log('calculating ' + input + ' x ' + input + '...');
        setTimeout(resolve, 500, input * input);
    });
}

// 0.5秒后返回input+input的计算结果:
function add(input) {
    return new Promise(function (resolve, reject) {
        log('calculating ' + input + ' + ' + input + '...');
        setTimeout(resolve, 500, input + input);
    });
}

var p = new Promise(function (resolve, reject) {
    log('start new Promise...');
    resolve(123);
});

p.then(multiply)
 .then(add)
 .then(function (result) {
    log('Got value: ' + result);
});
```

# 并行执行
除了串行执行若干异步任务外，Promise还可以并行执行异步任务。

试想一个页面聊天系统，我们需要从两个不同的URL分别获得用户的个人信息和好友列表，这两个任务是可以并行执行的，用Promise.all()实现如下：
```JavaScript
var p1 = new Promise(function (resolve, reject) {
    setTimeout(resolve, 500, 'P1');
});
var p2 = new Promise(function (resolve, reject) {
    setTimeout(resolve, 600, 'P2');
});
// 同时执行p1和p2，并在它们都完成后执行then:
Promise.all([p1, p2]).then(function (results) {
    console.log(results); // 获得一个Array: ['P1', 'P2']
});
```
那all()的具体实现是怎样的呢？
```javascript
function all(promises) {
  return new Promise(function(succeed, fail) {
    var results = [], pending = promises.length;
    promises.forEach(function(promise, i) {
      promise.then(function(result) {
        results[i] = result;
        pending -= 1;
        if (pending == 0)
          succeed(results);
      }, function(error) {
        fail(error);
      });
    });
    if (promises.length == 0)
      succeed(results);
  });
}
```
参考链接：
- [Promise](https://www.liaoxuefeng.com/wiki/001434446689867b27157e896e74d51a89c25cc8b43bdb3000/0014345008539155e93fc16046d4bb7854943814c4f9dc2000)
