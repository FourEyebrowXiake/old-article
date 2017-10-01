---
title: Javascript中的多态和继承
date: 2017-09-22 09:14:55
tags: Js基础
categories: Javascript
---
# 说点历史
了解Javascript的都知道，除了几个简单的数据类型，剩下的所有数据类型在Javascript中都是对象。之所以会这样，是因为Js诞生于面向对象思想最兴盛的时期。Js之父Brendan Eich无疑受到了影响。

面向对象的思想如此的火热，以至于出现了一些狂热的言论，如没用使用面向对象思想的程序都是过时的设计。这些言论当然是荒谬的，但不得不承认面向对象的变革推动了一些概念的流行。其中最重要的概念就是**封装**（将内部复杂性与外部接口切割开来)，而对象正是这一概念最鲜明的体现。

有了封装，才有了多态和继承等概念，可见其重要性。
<!-- more -->

# 封装
对象就像一个坚硬的外壳，它隐藏了其内部的复杂性，并提供给我们一些旋钮和接口（如方法）作为操作对象的接口。这一想法就是封装的具体体现。

# 多态
接口的多种不同的实现方法即为多态。

Javascript中多态是通过**组合**和**虚函数**来实现的。构造函数接受函数作为参数，来自定义自己的接口。

```Javascript
//父对象
function TextCell(text) {
  this.text = text.split("\n");
}
TextCell.prototype.minWidth = function() {
  return this.text.reduce(function(width, line) {
    return Math.max(width, line.length);
  }, 0);
};
TextCell.prototype.minHeight = function() {
  return this.text.length;
};
TextCell.prototype.draw = function(width, height) {
  var result = [];
  for (var i = 0; i < height; i++) {
    var line = this.text[i] || "";
    result.push(line + repeat(" ", width - line.length));
  }
  return result;
};

//子对象
function UnderlinedCell(inner) {
  this.inner = inner;
}
UnderlinedCell.prototype.minWidth = function() {
  return this.inner.minWidth();
};
UnderlinedCell.prototype.minHeight = function() {
  return this.inner.minHeight() + 1;
};
UnderlinedCell.prototype.draw = function(width, height) {
  return this.inner.draw(width, height - 1)
    .concat([repeat("-", width)]);
};

var underlinedCell = new UnderlinedCell(new TextCell(name));
```

如上，通过把TextCell对象传入UnderlinedCell构造函数进行组合。而像minHeight、 minWidth这类在父类和子类有不同实现的函数为虚函数。所以说Js中多态的实现靠组合和虚函数。

## 继承
当你像构造出与当前类型相似的类型时，可以通过继承来实现。

Js中的继承有多种实现，这里只介绍寄生组合式继承这一种。
```Javascript
// Shape - superclass
function Shape() {
  this.x = 0;
  this.y = 0;
}

// superclass method
Shape.prototype.move = function(x, y) {
  this.x += x;
  this.y += y;
  console.info('Shape moved.');
};

// Rectangle - subclass
function Rectangle() {
  Shape.call(this); // call super constructor.
}

// subclass extends superclass
Rectangle.prototype = Object.create(Shape.prototype);
Rectangle.prototype.constructor = Rectangle; // for rect instanceof Rectangle === true

var rect = new Rectangle();

console.log('Is rect an instance of Rectangle?', rect instanceof Rectangle);// true
console.log('Is rect an instance of Shape?', rect instanceof Shape);// true
rect.move(1, 1); // Outputs, 'Shape moved.'
```
其实呢，我们天天在用的 array、object 的继承关系和上面的代码如出一辙，看下图，Shape 相当于 Object，Rectangle 相当Array:

![](/images/Javascript中的多态和继承/1.png)
