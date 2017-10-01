---
title: Javascript语言精粹：方法（1）
date: 2017-09-12 22:43:04
tags: Javascript语言精粹
categories: Javascript
---
# 序
这篇文章包含一些数组API的实现，以及看书时的一些联想。

<!-- more -->
## 未解决的问题
- array的很多api的实现都基于.splice()。.splice()的优点在哪？希望知道的朋友能解答一下。
## Array
### array.slice(start, end) And array.concat(item...)
```javascript
var a = [1, 2, 3],

    c1 = [].concat(a),

    c2 = a.slice(0);
```
以上这两种copy方法在性能上相差无几。为了便于理解，推荐使用slice()。
### array.pop()、array.shift()、array.unshift()
pop的实现：
```javascript
Array.method('pop',function (){
	return this.splice(this.length-1,1)[0];
});
```
shift的实现：
```javascript
Array.method('shift',function() {
	return this.splice(0, 1)[0];
});
```
unshift的实现：
```javascript
Array.method('unshift', function() {
	this.splice.apply(
		this,
		[0, 0]
		.concat(Array.prototype.slice.apply(arguments))
	);
	return this.length;
});
```
### array.push(item...)
push的实现：
```javascript
Array.method('push',function (){
	this.splice.apply(
		this,
		[this.length, 0]
		.concat(Array.prototype.slice(arguments))
	);
	return this.length;
});
```
解释一波，分成三部分：
1. Array.prototype.slice(arguments)帮你把类数组转化成数组。如果你直接将arguments传入，arguments会被当作一个整体加入。原数组的长度只会加1：
- array
	- 0 ：“c”
	- 1 ：[Array(2), true, {...}] //arguments

也就是没有拆分好arguments。当然你可以通过arguments索引,再配上for一个一个地加入。但是多麻烦，直接用slice()多好。
2. [this.length,0].concat(#1)就是帮你产生一个新数组。新数组的前两位作为splice的前两个参数，即start和deletcount。
3. 最后一步，用this.splice.apply(this, #2)修改原数组。这里splice不是用来删除的，是用来添加的。

### array.sort(comparefun)
只能排序数字：
```javascript
num.sort(function(a, b){
	return a - b;
});
```
还能排序字符串：
```javascript
num_str.sort(function(a, b) {
	if(a === b) {
		return 0;
	}
	if(typeof a === typeof b) {
		return a < b ? -1 : 1;
	}
	return typeof a < typeof b ? -1 : 1;
});
```
再给力点，能排序数组对象：
```javascript
//by函数接受一个作为比较的成员名字符串参数。
//返回一个排序对象数组的比较函数
var by = function (name) {
	return function (o, p) {
		var a, b;
		if(typeof o === 'object' && typeof p === 'object' && o && p) {
			a = o[name];
			b = p[name];
			if( a === b ) {
				return 0;
			}
			if( typeof a === typeof b) {
				return a < b ? -1 : 1;
			}
			return typeof a < typeof b ? -1 : 1;
		} else {
			throw {
				name: 'Error',
				message: '期望通过'+name +'排序对象数组'
			};
		}
	};
};

var s = [
	{first: 'Joe',  last: 'Besser'},
	{first: 'Moe',  last: 'DeRita'}
];
s.sort(by('first'));
```
如果想基于多个键值进行排序：
```javascript
//只要修改：
if(a === b) {
	return typeof secondParam === 'function' ? secondParam (o, p) : 0;
}

s.sort(by('first',by('last')));
```
### array.reduce()
```javascript
Array.method('reduce', function(f, value) {
	var i;
	for(i =0; i<this.length; i++) {
		value =f(this[i],value);
	}
	return value;
});
```
### array.splice(start, deleteCount, item...)
splice 是我最喜欢的原生数组函数，只需要调用一次，就允许你删除元素、插入新的元素，并能同时进行删除、插入操作。需要注意的是，不同于`.concat和.slice,这个函数会改变源数组。

.splice会返回删除的元素。如果你想遍历已经删除的数组时，这会非常方便。
