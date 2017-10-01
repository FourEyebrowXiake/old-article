---
title: Js基础：Array、String的常用方法
date: 2017-07-22 16:01:22
tags: Js基础
categories: Javascript
---
# Array

- ECMAScript数组的大小是可以动态调整的，既可以随着数据的添加自动增长以容纳新增数据。当把一个值放在超出当前数组大小的位置上时，数组就会重新计算长度值，即长度值等于最后一项的索引加一，前面的值都自动被赋值为了undefined了。

### Array.prototype.concat()
concat() 方法用于合并两个或多个数组。此方法不会更改现有数组，而是返回一个新数组。
```javascript
var one=['a','b'];
var two=['d','c'];
var three=one.concat(two);//three[a,b,c,d] one[a,b] two[c,d]
```
#### 描述
> concat 方法将创建一个新的数组，然后将调用它的对象(this 指向的对象)中的元素以及所有参数中的数组类型的参数中的元素以及非数组类型的参数本身按照顺序放入这个新数组,并返回该数组.
>
> concat 方法并不修改调用它的对象(this 指向的对象) 和参数中的各个数组本身的值,而是将他们的每个元素拷贝一份放在组合成的新数组中.原数组中的元素有两种被拷贝的方式:

* 对象引用(非对象直接量):concat 方法会复制对象引用放到组合的新数组里,原数组和新数组中的对象引用都指向同一个实际的对象,所以,当实际的对象被修改时,两个数组也同时会被修改.
* 字符串和数字(是原始值,而不是包装原始值的 String 和 Number 对象): concat 方法会复制字符串和数字的值放到新数组里.

简单的说
1. concat不修改原数组
2. concat是浅复制，只是对于数组包含的元素的复制。

<!-- more -->

### 队列、堆栈
### Array.prototype.shift()
shift() 方法从数组中删除第一个元素，并返回该元素的值。此方法更改数组的长度。
#### Array.prototype.unshift()
The unshift() method adds one or more elements to the beginning of an array and returns the new length of the array.
####  Array.prototype.push()
The push() method adds one or more elements to the end of an array and returns the new length of the array.
#### Array.prototype.pop()
The pop() method removes the last element from an array and returns that element. This method changes the length of the array.
### Array.join(separator)
数组格式化输出，返回字符串。以 separator 分隔，separator 默认为','
```javascript
[1,2,3].join(' ') // '1 2 3'
```
### Array.prototype.slice(begin,end)
返回数组中的一部分的浅复制到一个新的数组对象，只对数组中的元素进行浅复制。原始数组不会被修改。
```javascript
var a = ['zero', 'one', 'two', 'three'];
var sliced = a.slice(1, 3);

console.log(a);      // ['zero', 'one', 'two', 'three']
console.log(sliced); // ['one', 'two']
```
### Array.prototype.splice(start, deleteCount, items...)
splice() 方法通过删除现有元素和/或添加新元素来更改一个数组的内容。

### 高阶函数
### Array.prototype.forEach()
forEach() 方法对数组的每个元素执行一次提供的函数。
### Array.prototype.reduce()
reduce() 方法对累加器和数组中的每个元素 (从左到右)应用一个函数，将其减少为单个值。
```javascript
[0, 1, 2, 3, 4].reduce(function(accumulator, currentValue, currentIndex, array){
  return accumulator + currentValue;
});// 10
```

#### 描述
reduce 为数组中的每一个元素依次执行回调函数，不包括数组中被删除或从未被赋值的元素，接受四个参数：

* accumulator 初始值（或者上一次回调函数的返回值）
* currentValue 当前元素值
* currentIndex 当前索引
* array 调用 reduce 的数组。

回调函数第一次执行时，accumulator 和 currentValue 的取值有两种情况：调用 reduce 时提供initialValue，accumulator 取值为 initialValue ，currentValue 取数组中的第一个值；没有提供 initialValue ，accumulator 取数组中的第一个值，currentValue 取数组中的第二个值。

### Array.prototype.map()
map() 方法创建一个新数组，其结果是该数组中的每个元素都调用一个提供的函数后返回的结果。

callback 函数会被自动传入三个参数：当前数组元素，当前元素索引，原数组本身

使用 map 方法处理数组时，数组元素的范围是在 callback 方法第一次调用之前就已经确定了。在 map 方法执行的过程中：原数组中新增加的元素将不会被 callback 访问到；若已经存在的元素被改变或删除了，则它们的传递到 callback 的值是 map 方法遍历到它们的那一时刻的值；而被删除的元素将不会被访问到。
```javascript
var numbers = [1, 4, 9];
var roots = numbers.map(Math.sqrt);
// roots的值为[1, 2, 3], numbers的值仍为[1, 4, 9]
```
#### Array.prototype.filter()
The filter() method creates a new array with all elements that pass the test implemented by the provided function.

```javascript
var words = ["spray", "limit", "elite", "exuberant", "destruction", "present"];

var longWords = words.filter(function(word){
  return word.length > 6;
})

// Filtered array longWords is ["exuberant", "destruction", "present"]
```
###### [数组的其他方法和属性]（https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array）



# String
### string.charAt(pos)

返回只含一个字母的字符串

### string.indexOf(searchString, position)

返回字符串里面的字母所在的位置下标，实际上把 string 看成一个 char[]，更容易理解

### string.slice(start, end)

返回子字符串，参数可以为负数

### string.match(regexp)

返回比配字符串，假如有正则表达式为/xxx/g则，返回一个数组，否则返回第一次匹配到的字符串

### string.replace(searchValue, replaceValue)

替换字符串，searchValue 可以为正则表达式。
