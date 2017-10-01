---
title: 'Javascript语言精粹: 数组'
date: 2017-09-11 10:47:27
tags: Javascript语言精粹
categories: Javascript
---
# 序
在C++、Java中，数组是一段线性分配的内存。它通过整数计算偏移并访问其中的元素。这样的数组是一种性能出色的数据结构。可惜，javascript中没有像此类数组一样的数据结构。

javascript中的数组是一个拥有数组特性的对象。它把数组的下标转换成字符串，用其作为属性。这使它明显比一个真正的数组慢，但它使用起来更方便。它的属性的检索和更新的方式与对象一样，只是多了一个可以用整数作为属性名的特性。

<!-- more -->

---

# 数组基础
## 数字字面量
像对象、函数都有字面量一样，数组也有它的字面量。
```javascript
var empty = [];
var numbers = [ 'zero', 'one', 'two', 'three'];

empty[1] //undefined
numbers[1]  //one

empty.length //0
numbers.length //4
```
我开头就说过javascript中的数组也是对象。现在就用对象字面量创建一个数组：
```javascript
var numbers_object = [
	'0': 'zero', '1': 'one', '2':'two', '3':'three'
]
```
两者产生的结果相似。但是两者有着显著的不同。numbers继承Array.prototype,numbers_object继承Object.prototype。除此之外，numbers还有个length属性。

说到length，不得不说javascript的length很奇葩。为什么，接着看。

## 长度
-  javascript中的length是没有上界的。length会随着数组容量的增大，而自动增大，不会发送数组越界错误。不过ECMAScript262的标准规定，数组的下标必须在0到231之间。
- 你可以直接设置length值。什么情况，自己设置length。对，想想length只是个属性而已，就没什么奇怪了。length设得过大，不会给数组分配更多的空间。如果设小了，会删除下标大于等于新length的属性。

## 容易混淆的地方
#### 既然数组是对象，拿什么时候用数组，什么时候用对象呢？

当属性名是小而连续的整数时，你应该使用数组。否则，使用对象。
#### 怎么区分数组和对象呢？
```javascript
	var is_array = function (value) {
		return Object.prototype.toString.apply(value) === '[object Array]';
	};
```
---

# 多维数组和初始化
## 初始化
javascript的数组通常不会预置值。如果你需要预置值的话，要自己准备：
```javascript
Array.dim = (len, init) => {
	var a=[], i;
	for (i=0; i<len; i++) {
		a[i] = init;
	}
	return a;
}
```
## 多维数组
javascript没有多维数组，但是就像大多数C语言一样，它支持元素为数组的数组。
```javascript
//构造多维数组
Array.matrix = (m, n, init) => {
	var a, i, j, mat = [];
	for (i=0; i<m; i++) {
		a = [];
		for(j=0; j<n; j++) {
			a[j] = init;
		}
		mat[i] = a;
	}
	return mat
}
//构造一个初始值为0的4X4矩阵。
var myMatrix = Array.matrix(4, 4, 0);
```
