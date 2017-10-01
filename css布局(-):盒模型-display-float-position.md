---
title: 'css布局(-):传统'
date: 2017-08-24 10:25:52
tags: css基础
categories: CSS
---
参考链接：

- [http://zh.learnlayout.com/](http://zh.learnlayout.com/)

这个网站真的很棒。实例好，讲解清楚。不急的话，可以直接看他的。

不过我关于浮动和清除的介绍比他详细点，另外还有重置样式表。别忘了回来哦。

# 传统
布局的传统解决方案，基于盒状模型，依赖 display 属性 + position属性 + float属性。它对于那些特殊布局非常不方便，比如，垂直居中就不容易实现。

这篇主要介绍一下传统布局，下篇将Flex。

<!-- more -->

## 盒模型
每一个元素都会在页面上生成一个盒子。因此，HTML 页面实际上就是由一堆盒子组成的。

在CSS中，使用标准盒模型描述这些矩形盒子中的每一个。这个模型描述了元素所占空间的内容。每个盒子有四个边：外边距边, 边框边, 内填充边 与 内容边。

### box-sizing
当你设置一个元素为 box-sizing: border-box; 时，此元素的内边距和边框不再会增加它的宽度。

## 重置样式表
CSS reset的作用是让各个浏览器的CSS样式有一个统一的基准，而这个基准更多的就是“清零”！
```HTML
//一般网站的推荐写法
body, dl, dd, h1, h2, h3, h4, h5, h6, p ,form {
	margin:0;
}
ol,ul {
	margin:0; padding:0;
}
```
---

## 浮动和清除
### 浮动
浮动，你看这俩字儿多形象，意思就是把元素从常规文档流中拿出来。

拿出 来干什么?一是可以实现传统出版物上那种文字绕排图片的效果，二是可以让原来 上下堆叠的块级元素，变成左右并列，从而实现布局中的分栏。

当你浮动一个元素的时候......这些(浮动)规则就好像 在说:“尽量把这个元素往上放，能放多高放多高，直到碰到某个元素的边界为止。”

### clear
clear 属性被用于控制浮动。

clear CSS 属性指定一个元素是否可以在它之前的浮动元素旁边，或者必须向下移动(清除浮动) 在它的下面。clear 属性适用于浮动和非浮动元素。

### 清除浮动
浮动元素脱离了文档流，其父元素也看不到它了，因而也不会包围它。父元素会收缩到没它一样。这往往混乱页面的布局。所以我们需要使父元素围住浮动元素。

那怎么办呢？

1. 通过简单的给父元素加上 class="clearfix" 做到。
```css
.clearfix {
  overflow: auto;
}
```
这个可以在现代浏览器上工作。如果你想要支持IE6，你就需要再加入如下样式：
```css
.clearfix {
  overflow: auto;
  zoom: 1;
}
```
2. 没有父元素时，使用clear
通过给元素应用clear：both，强迫它定位在前一个浮动元素的下方。

2. 没有父元素，且空间足以容纳多个元素上浮动时，还是使用.clearfix大法。不过这次是给子元素用。
```css
.clearfix:after {
    content:".";
    display:block;
    height:0;
    visibility:hidden;
    clear:both;
}
```
它只添加一个清除的包含句点为非浮动元素。规则中的其他声明只是为了确保这个伪元素没有高度，且不可见。就像有条看不见的分割线，拦住了从下往上冲的元素。

---

## Display
display 是CSS中最重要的用于控制布局的属性。每个元素都有一个默认的 display 值，这与元素的类型有关。对于大多数元素它们的默认值通常是 block 或 inline 。一个 block 元素通常被叫做块级元素。一个 inline 元素通常被叫做行内元素。

### none
另一个常用的display值是 none 。一些特殊元素的默认 display 值是它，例如 script 。 display: none 通常被 JavaScript 用来在不删除元素的情况下隐藏或显示元素。

它和 visibility 属性不一样。把 display 设置成 none 元素不会占据它本来应该显示的空间，但是设置成 visibility: hidden; 还会占据空间。

### inline-block
一种创建网格布局的简单方法。
> 你得做些额外工作来让IE6和IE7支持 inline-block 。有些时候人们谈到 inline-block 会触发叫做 hasLayout 的东西，你只需要知道那是用来支持旧浏览器的。如果你对此很感兴趣，可以在前面那个链接中找到更详细的信息。否则我们就继续下去吧。

---

## Position

### static
static 是默认值。任意 position: static; 的元素不会被特殊的定位。一个 static 元素表示它不会被“positioned”，一个 position 属性被设置为其他值的元素表示它会被“positioned”。
### relative
relative 表现的和 static 一样，除非你添加了一些额外的属性。

在一个相对定位（position属性的值为relative）的元素上设置 top 、 right 、 bottom 和 left 属性会使其偏离其正常位置。其他的元素的位置则不会受该元素的影响发生位置改变来弥补它偏离后剩下的空隙。

### fixed
一个固定定位（position属性的值为fixed）元素会相对于视窗来定位，这意味着即便页面滚动，它还是会停留在相同的位置。和 relative 一样， top 、 right 、 bottom 和 left 属性都可用。

### absolute
absolute 是最棘手的position值。 absolute 与 fixed 的表现类似，但是它不是相对于视窗而是相对于最近的“positioned”祖先元素。如果绝对定位（position属性的值为absolute）的元素没有“positioned”祖先元素，那么它是相对于文档的 body 元素，并且它会随着页面滚动而移动。记住一个“positioned”元素是指 position 值不是 static 的元素。
