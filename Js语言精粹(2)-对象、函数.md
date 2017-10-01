---
title: Javascript语言精粹：对象、函数
date: 2017-08-20 12:59:12
tags: Javascript语言精粹
categories: Javascript
---
# 对象
Javascript的简单数据类型包括数字、字符串、布尔值、null值和undefined值。其他所有的值皆是对象，包括数组、函数和正则表达式。

Javascript中的对象是可变的键控集合。对象是属性的容器，其中属性由键值对构成。

Javascript包含一种原型链的特性，允许对象继承另一个对象。正确的使用它能减少对象初始化时消耗的时间和内存。

ES6中新加入了Class。但这个Class只是简洁了Javascript的语法，让人看上去更清爽。详情见[深入浅出ES6（十三）：类 Class](http://www.infoq.com/cn/articles/es6-in-depth-classes?utm_source=articles_about_ES6-In-Depth&utm_medium=link&utm_campaign=ES6-In-Depth)

<!-- more -->

## 对象字面量
一个对象字面量就是包围在一对花括号中的零或多个键值对。对象还是可嵌套的。
```Javascript
var flight = {
	airline_name: "Oceanic",
	"airline-number": 815,
	departure: {
		IATA: "SYD",
		time: "2017-08-20 13:27",
		city: "HangZhou"
	}
}
```

## 检索属性

有两种方式：. 表示法，或object[attr]数组访问法，各有用途。

- .表示法用于当要检索的属性为不变常量时，可读性更好，强烈推荐用。

- 数组访问法在属性为字符串变量时，必用（此时点表示法失效）。

```Javascript
	stooge["first-name"]
	flight.departure.IATA
```
## 引用
引用传值都是浅复制。
- 当你需要某个对象的一份拷贝时，一定要用深复制。
```javascript
//对于 JSON 安全的对象(只有由对象和数组组成的对象)，我们的深复制非常简单
var newObj = JSON.parse(JSON.stringify(obj));
```
- 当你需要共享某一个对象时，即需要多个变量对同一个对象进行操作，一定要用浅复制

```Javascript
	_.clone = function(obj) {
		if (_.isObject(obj)) return obj;
		return _.isArray(obj) ? obj.slice : _.extend({},obj);
	};
```

## 反射
反射用于描述能够检查同一系统（或其自身）中的其他代码的代码。

简单点说。在你不知道一个对象内部结构的情况下(你甚至连这个对象的一个属性，或api都不知道)，怎么跟他通信呢？这个时候就要通过一种机制，得到它的内部结构。这种机制就叫做反射。说白了，窥探对象内部结构的方法就叫做反射。

typeof和hasOwnProperty都是很好的例子。

### 枚举
#### for in
for in 的迭代无序，且包括原型链。虽然可以通过hasOwnProperty()和typeof来过滤.
### 删除
```Javascript
delete another.name;
```
---
# 函数
函数包含一组语句，是js 的基础模块单元，用于代码复用、信息隐藏和组合调用。函数用于制定对象的行为。

## 函数对象
除了一般函数，其他所有的构造器都来自于Function.prototype，甚至包括根构造器Object及Function自身.

Function构造器产生函数对象，会运行类似这样的代码：
```Java
	this.prototype = {
		constructor: this
	};
```

- Function.prototype的__proto__指向 Object.prototype, Object.prototype.proto === null

- .prototype是一个对象的原型对象,而__proto__则是对原型对象的引用。

- Object实际上是一个函数，所以可以Object()。

- 函数的与众不同之处在于他们可以被调用。

## 函数字面量

```Javascript
var add = function (a,b) {
	return x + y ;
};
```

通过函数字面量创建的函数对象包含一个连到外部上下文连接。这被称为闭包.

# 调用
this的值取决于调用的模式，因为this是延迟绑定的。this到对象的绑定发生在调用时。
## 方法调用模式
this绑定调用方法的对象。
```javascript
var myObject = {
	value: 0,
	increment: function (inc) {
		return this.value += typeof inc === 'number' ? inc : 1;
	}
};
myObject.increment(1);
```

## 函数调用模式
当一个函数不是对象的属性时，他就是被当作函数来调用。此时this绑定到全局对象.
```javascript
var sum = add(3,4);
```
这样直接导致的后果是add方法不能利用内部函数来帮助它工作。因为add的内部函数的this被绑定了错误的值，所以add不能访问对象的属性。

好在可以通过给add方法传入赋值了this的变量，来解决。

解决办法：
```Javascript
myObject.double = function () {
	var that = this;
	var helper = function () {
		that.value = add(that.value,that.value);
	};
	helper();
}

myObject.double();
document.writeln(myObject.value);
```

## 构造器调用模式
javascript对原型继承不自信的表现。

如果在一个函数前面带上 new 调用，那么背地里将会创建一个新对象。新对象连接到该函数的prototype成员。
```javascript
var Quo = function (string) {
	this.status = string;
}
Quo.prototype.get_status = function () {
	return this.status;
};
var myQuo = new Quo('happy');
document.writeln(myQuo.get_status());
```
## Apply调用模式
javascript是一门函数式的面向对象编程语言，所以函数可以拥有方法。

apply方法帮助我们构建一个参数数组传递给调用函数。它能帮助函数设置this。
```javascript
var statusObject = {
	status: 'OK'
};
//把get_status方法内的作用域换成statusObject的作用域。
//即使statusObject没有继承自Quo.prototype,但我们可以在statusObject上调用get_status.
var status = Quo.prototype.get_status.apply(statusObject);
//status的值为OK。
```

call()方法的作用和 apply() 方法类似，只有一个区别，就是 call()方法接受的是若干个参数的列表，而apply()方法接受的是一个包含多个参数的数组。

## 扩充类型功能
Javascript允许给语言的基本类型扩充功能。而且给原型扩充时，是动态的。
```javascript
Function.prototype.method = function(name, func){
	if(!this.prototype[name]){//如果方法已存在，就不添加
			this.prototype[name] = func;
	}
	return this;
};
```
---

## 递归
递归函数就是会直接或间接调用自身的函数。它把一个问题分为一组相似的子问题，每一个都用一个寻常解去解决。

javascript解决汉诺塔问题：
```javascript
var  hanoi = function(num，one, two，three){
	if(num > 0){
		hanoi(num - 1, one, three, two)
		document.writeln('MOVE num'+num+' from '+one+' to '+ three);
		hanoi(num-1,three, one, two)
	}
}
```

##作用域、闭包
[见我的另一篇文章](http://foureyebrowxiake.github.io/2017/07/22/Js语法基础：作用域、闭包、this/)

## 回调函数
[回调函数（callback）是什么？ - 回答作者: no.body]( https://zhihu.com/question/19801131/answer/27459821)

## 级联
返回this的方法，就可以启动级联。

## 记忆
一个函数，它能帮我们构造带记忆功能的函数。
```javascript
var memorizer = (memo, formula) => {
	var recur = (n) => {
		var result = memo[n];
		if(typeof result !== 'result') {
			result = formula(recur, n);
			memo[n] = result;
		}
		return result;
	}
	return recur;
}
```
