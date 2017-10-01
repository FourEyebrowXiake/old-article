---
title: Javascript语言精粹：序、精华与糟粕
date: 2017-08-19 14:18:41
tags: Javascript语言精粹
categories: Javascript
---
# 序
本文是《Javascript语言精粹》的笔记和思考。

# 为什么要使用Javascript
1. 你没有其他选择。web已变成一个重要的应用开发平台，而javscript是唯一一门所有浏览器都可以识别的语言。
2. javascript虽然有缺陷，但它真的很优秀。它既轻量又富有表现力。而且，你一旦掌握了它，就会发现函数式编程的魅力。
任何语言都有精华和糟粕。我们要取其精华，弃其糟粕。况且，你有权利定义你自己的子集。我们完全可以基于精华部分去编写更好的程序。

<!-- more -->

# Javascript优点
- 是弱类型语句。
- 有非常强大的对象字面量表示法。
- 基于原型继承的动态对象。
- 函数是顶级对象。函数可以调用方法。Javascript的继承可以借此特性实现。

# Javascript缺点以解决办法
使用 jshint 或者其他的一些 linter 插件工具来避免自己使用糟糕的特性。
#### 全局变量
> javascript依赖全局变量来进行连接。所有编译单元的所有顶级变量被撮合到名为“全局变量”的公共命名空间中。

可以使用es6的let，IIFE等方法解决。
#### +
> +的两个运算数只要一个不是 number 类型，+ 就会做字符串拼接。

#### parseInt(str, int)
> parseInt(str, int) 遇到非数字会自动停止解析，请一定加上第二个参数来设置进制.

不然假如第一个参数是零开头就会以8进制求值，比如"08"，返回零，因为8进制没有"8"

#### 浮点数
0.1 + 0.2 不等于0.3
```javascript
0.3 - 0.1    // 0.19999999999999998
```

使用js库 https://github.com/MikeMcl/decimal.js
```javascript
x = new Decimal(0.3)
x.minus(0.1)                  // '0.2'
x                             // '0.3'
```
#### 判断value是不是数字
```javascript
Number.isFinite('0'); //false
```
和全局的isFinite()函数相比，这个方法不会强制将一个非数值的参数转换成数值，这就意味着，只有数值类型的值，且是有穷的（finite），才返回 true。

```javascript
Number.isNaN(NaN);        // true
Number.isNaN(Number.NaN); // true
Number.isNaN(0 / 0)       // true
// 下面这几个如果使用全局的 isNaN() 时，会返回 true。
Number.isNaN("NaN");      // false，字符串 "NaN" 不会被隐式转换成数字 NaN。
Number.isNaN(undefined);  // false
Number.isNaN({});         // false
```
和全局函数 isNaN() 相比，该方法不会强制将参数转换成数字，只有在参数是真正的数字类型，且值为 NaN 的时候才会返回 true。

#### js没有真正的数组
```javascript
typeof [] // 'object'

//检测一个值是不是数组
function isArray(value) {
    return Object.prototype.toString.apply(value) === '[object Array]'
}
```
#### null和undefined的区别
null表示一个空值对象，即不存在的对象。当声明的变量还未被初始化时，变量的默认值为undefined。null还是一个对象，undefined就表示没有。


null典型用法是：
- 作为函数的返回值，表示该函数本应该返回对象，但是找不到，即为不存在的对象的意思。
- 作为对象原型链的终点。
- 调用函数时，作为缺省的参数。比如
```javascript
Math.max.apply(null, array);
```
undefined 典型用法是：

- 变量或对象属性被声明了，但没有赋值时，就等于 undefined。
- 调用函数时，未传入参数，函数内部该参数等于 undefined。
- 函数没有返回值时，默认返回 undefined。
```javascript
Object.getPrototypeOf(Object.prototype); // null
```
## 参考链接:
- [js thinking(1):序、精华与糟粕、对象](https://github.com/SimplyY/blog/blob/master/Article/%E7%BC%96%E7%A8%8B/web%20%E5%89%8D%E7%AB%AF/js%20thinking/js%20thinking(1)%3A%E5%BA%8F%E3%80%81%E7%B2%BE%E5%8D%8E%E4%B8%8E%E7%B3%9F%E7%B2%95%E3%80%81%E5%AF%B9%E8%B1%A1.md)
