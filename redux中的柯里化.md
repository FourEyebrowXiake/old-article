---
title: redux-thunk中的柯里化
date: 2017-08-28 09:45:38
tags: Redux
categories: Javascript
---
参考链接：
- [why curry helps](https://gist.github.com/jcouyang/b56a830cd55bd230049f)

# Thunk
redux中实现异步操作的之一就是使用redux-thunk.redux-thunk中间件改造store.dispatch，使得后者可以接受函数作为参数。
它的源码：
```javascript
const thunk = (store) => (next) => (action) =>
  typeof action === 'function' ?
    action(store.dispatch) :
    next(action);
```
其实以上就是个柯里化函数，只不过使用了es6的写法。但是看着这么多箭头，我一开始是很懵逼的。不过，只要清楚柯里化就能轻松理解上面的代码。

<!-- more -->

# 什么是柯里化
函数也是值。从而我们可以用有趣的方法去操作函数。柯里化允许我们把函数与传递给它的值相结合，产生一个新的函数。

换种说法就是把接受多个参数的函数变换成接受一个个单一参数的函数，并且在最后返回（接受余下的参数而且返回结果的）新函数的技术。

## 柯里化的具体实现
对于一个原本需要n个参数的函数，柯里化就是把第一个参数变量存在函数里面，然后返回一个只需要(n-1)个参数的函数。比如：
```javascript
let add = x => y => x + y
let add2 = add(2)
```
本来完成 add 这个操作，应该是:
```javascript
let add = (x, y) => x + y
```
它需要俩参数，而现在 add2 函数完成同样操作只需要一个参数，这在函数式编程中广泛应用。

详细解释一下，就是 add2 函数 等价于 有了 x 这个闭包变量的 y => x + y 函数

并且此时 x = 2，所以此时调用
```javascript
add2(3) === 2 + 3
```

## 连续箭头
搞清楚里一个箭头，明白n个箭头也不难。n 个连续箭头组成的函数实际上就是柯里化了 n - 1次。

##### 函数里的具体调用过程：
前 n - 1 次调用，其实是提前将参数传递进去，并没有调用最内层函数体，最后一次调用才会调用最内层函数体，并返回最内层函数体的返回值。

## 为什么要用柯里化
知道了柯里化的具体实现。你有没有想过，为什么要用它呢？柯里化不就是返回一个部分配置好的函数吗？

### 柯里化函数接口
```javascript
//before
var multiple = function(a, b){
  return +a*b + ''
}
var concatArray = function(chars, inc){
  return chars.map(function(char){
  	return multiple(char, inc);
  }).reduce(function(a,b){
      return a.concat(b)
  });
}
console.log(concatArray(['1','2','3'], 2))
```
上面的例子的map函数是为了把所有的数字加1，再乘以2。如果我还需要数字都减2呢？是不是又要重新写multiple。是不是很麻烦，那怎么能把他提取出来呢？来对比下柯里化的解法：
```javascript
//after
var multiple = function(a){
  return function(b){
    return +b*a + ''
  }
}

var plus = function(a){
  return function(b){
    return (+b)+a + ''
  }
}
var concatArray = function(chars, stylishChar){
  return chars.map(stylishChar)
    .reduce(function(a,b){
      return a.concat(b)
  });
}
console.log(concatArray(['1','2','3'], multiple(2)))
console.log(concatArray(['1','2','3'], plus(2)))
```
有什么不一样呢

1. 处理数组中字符的函数被提取出来, 作为参数传入
2. 提取成柯里化的函数, 部分配置好后传入, 好处显而易见, 这下接口非常通畅 无论是外层调用

```javascript
concatArray(['1','2','3'], multiple(2))
```
还是内部的 map 函数
```javascript
chars.map(stylishChar)
```
chars.map(stylishChar)
这些接口都清晰了很多, 不是吗

**这就是函数式的思想, 用已有的函数组合出新的函数, 而柯里化每消费一个参数, 都会返回一个新的部分配置的函数, 这为函数组合提供了更灵活的手段, 并且使得接口更为流畅.**

再加上自动柯里化的库 ramda, 简直就完美了

var multiple = ramda.curry(function(a, b){
  return +b*a + ''
})
var plus = ramda.curry(function(a, b){
  return (+b)+a + ''
})
```javascript
JS Bin<script src="http://static.jsbin.com/js/embed.js"></script>
```

# 回归正题
现在回头看thunk的源码。

它只不过是提前传递了store，供函数形式的action使用。以及next，代表下一个中间件。而最后一个参数'action'像任何其他中间件一样。

不然的话，我们只是把结果传递给链中的下一个中间件而已。
