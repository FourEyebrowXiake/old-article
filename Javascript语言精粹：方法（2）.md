---
title: Javascript语言精粹：方法（2）
date: 2017-09-13 16:42:41
tags: Javascript语言精粹
categories: Javascript
---
# 序
文章包含Function、Number和Object的一些常用API的实现和说明。

<!-- more -->

## Function

### function.bind(thatArg)
```javascript
Function.method('bind',function (that) {
	var  method = this,
				slice = Array.prototype.slice,
				 args = slice.apply(arguments,[1]);//获得除this以外的参数
				 return function() {
					 return method.apply(that,
					 args.concat(slice.apply(arguments, [0])));
				 };
})
```
#### apply、call
apply或call是用来指定某个方法的作用对象的。换句话说，就是为了改变函数体内部 this 的指向。

像“slice.apply(arguments,[1])”这样的语句，使arguments拥有了slice方法，虽然arguments只是个类数组。

所以，可以看出 call 和 apply 是为了动态改变 this 而出现的，当一个 object 没有某个方法（如arguments），但是其他的有（上面的例子中Array有slice方法），我们可以借助call或apply用其它对象的方法来操作。

#### bind
我们先看一下MDN的解释：
> bind()方法会创建一个新函数，称为绑定函数，当调用这个绑定函数时，绑定函数会以创建它时传入 bind()方法的第一个参数作为 this，传入 bind() 方法的第二个以及以后的参数加上绑定函数运行时本身的参数按照顺序作为原函数的参数来调用原函数.

js的世界中，函数内的this会因为调用方式的不同，而发生改变。所以,常使用bind来固定函数的上下文。当然，你也可以通过函数内的变量保存上下文，来达到相同的效果。

另外，一个函数多次调用bind方法，函数内的this一直是第一次绑定的值，不会改变。

#### apply、call和bind的区别

那么 apply、call、bind 三者相比较，之间又有什么异同呢？何时使用 apply、call，何时使用 bind 呢。简单的一个例子：
```javascript
var obj = {
    x: 81,
};

var foo = {
    getX: function() {
        return this.x;
    }
}

console.log(foo.getX.bind(obj)());  //81
console.log(foo.getX.call(obj));    //81
console.log(foo.getX.apply(obj));   //81
```

三个输出的都是81，但是注意看使用 bind() 方法的，他后面多了对括号。

也就是说，区别是，当你希望改变上下文环境之后并非立即执行，而是回调执行的时候，使用 bind() 方法。而 apply/call 则会立即执行函数。

再总结一下：
- apply 、 call 、bind 三者都是用来改变函数的this对象的指向的；
- apply 、 call 、bind 三者第一个参数都是this要指向的对象，也就是想指定的上下文；
- apply 、 call 、bind 三者都可以利用后续参数传参；
- bind 是返回对应函数，便于稍后调用；apply 、call 则是立即调用 。

---

## Number
### number.toFixed(fractionDigits)
toFixed方法把number转换成为一个十进制的字符串。fractionDigits这个参数用来设置保留几位小数。
### number.toPrecision(precision)
和toFixed不同的是，toPrecision决定数字的长度。toFixed决定的是小数点后的长度。
### number.toString(radix)
参数radix用来控制返回的number字符串的进制。

---

## Object
### Object.hasOwnProperty(name)
该方法用来判断对象是否包含name属性。原型链不会被检查到。这个方法对name是hasOwnProperty的无效，此时返回false。

---

## 参考链接
- [深入浅出妙用 Javascript 中 apply、call、bind](http://web.jobbole.com/83642/)
