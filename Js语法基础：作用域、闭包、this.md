---
title: Js基础：作用域、闭包、this
date: 2017-07-22 16:12:26
tags: Js基础
categories: Javascript
---
# 作用域
## 词法作用域：
描述了源代码中的范围。

if语句或while语句以及其他类似循环结构的语句块不能创建新的作用域。只用function语句块能创建新的作用域。
## 执行环境：
当程序运行的时候，会创建一个存储变量和变量值的存储系统。这些内存中的作用域结构被称为执行环境。

## 执行环境和词法作用域的不同：
执行环境实在程序运行时才被创建的，不是在代码输入时被创建的。执行环境能控制在程序执行过程中不同点能访问哪些变量。
注：
```javascript
var test=function(){};
var test1=test();
var test2=test();
log(test1===test2);//false
javascript不对对象进行引用
```

<!-- more -->

# 闭包
闭包是指一些函数通过某种方式可以随时被访问，即使它的外部代码已经执行完毕。

在本质上，闭包就是将函数内部和函数外部连接起来的一座桥梁。

常见的闭包的现象，如通过函数字面量创建的函数对象包含一个连到外部上下文的连接。
```javascript
var hello = function (a，b) {
	return a + b；
}
```

## 保留函数访问权限的方法

* passing to setTimeout
* 从函数中return
```javascript
function f1(){
　　　　var n=999;//只要内部函数需要，n会持续保留
　　　　function f2(){
　　　　　　alert(n);
　　　　}
　　　　return f2;
　　}
　　var result=f1();
　　result(); // 999
```

* 保存为全局变量

## 一个重要的问题
javascript的内部函数访问外部函数的实际变量而无须复制。这个特性，常常会引发一类错误：
```javascript
var add_the_handlere = (nodes) => {
	for (var i = 0; i< nodes.length; i++){
		nodes[i].onclick = (e) => {
			alert(i);
		};
	}
};
```
因为内部函数访问外部函数的实际变量而无须复制，所以“alert(i)”中的i绑定的是外部i本身，而不是函数构造时的变量值。这就导致每个事件处理器的i都相同。

## 用处

1. 设计模块中的私有属性和方法（闭包中的自由变量无法被直接访问）
2. 让这些变量的值始终保持在内存中(可以被延迟调用，比如事件 handler，ajax 异步调用的回调函数等)。


---

# This

this指调用函数的那个对象

## 使用new的时候会发生什么

1. 创建一个新的空对象；//Object.create();
2. 将this绑定到该对象；
3. 添加一个名为__proto__的新属性，并且指向构造函数的原型(prototype)；
4. 返回该this对象。//return this;

## this的用法
1. 纯粹的函数调用: this 就代表全局对象 Global（浏览器下就是 window）。
2. 作为对象方法的调用: this 指向调用方法的对象。
3. 作为构造函数调用：this 就指向 构造器创建的新对象
4. apply, call, bind调用：this 指向就是这些函数的第一个参数
---
# 原型链
当查找一个对象的属性时，JavaScript 会向上遍历原型链，直到找到给定名称的属性为止（这种行为我们也称之为委托）。

原型链是使用其他对象属性的一种方式。

原型链的属性复制发生在当前查找的时候，所以是实时更新的。

### 推荐阮一峰老师的这篇文章：

[Javascript为什么会有prototype](http://www.ruanyifeng.com/blog/2011/06/designing_ideas_of_inheritance_mechanism_in_javascript.html)
