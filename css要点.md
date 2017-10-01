---
title: HTML和CSS杂记
date: 2017-08-09 20:38:44
tags: css基础
categories: CSS
---
# CSS要点
《css设计指南》笔记

<!-- more -->

## HTML
### section

只有元素内容会被列在 文档大纲 中时，才适合用section元素。

这里的文档大纲更像是提纲，section 就是提纲中的不重复项（比如对于博文 list，整个 list 才属于一个 section）

### 块级元素和行内元素

块级元素盒子会扩展到与父元素同宽 明确设定 width 属性后，块级元素就不会再扩展到与父元素(即 body)同宽了。没有设定 width，值为 auto。
行内元素盒子会“收缩包裹”其内容，并且会尽可能包紧。
## CSS
### 伪类
伪类用于当已有元素处于的某个状态时，为其添加对应的样式，这个状态是根据用户行为而动态变化的。
- UI伪类会在 HTML 元素处于某个状态时(比如鼠标 指针位于链接上)，为该元素应用 CSS 样式。
- 结构化伪类会在标记中存在某种结构上的关系时(如某个元素是一组元素中的第一个或最后一个)，为相应元素应用 CSS 样式。
#### UI伪类
- 链接伪类(link 未被点，visited 已被点，hover 鼠标悬停，active 正在被点)
- :focus 伪类 表单中的文本字段在用户单击它时会获得焦点，获得焦点时使用 css 样式。
- :target 伪类，用户点击一个指向页面中其他元素的链接。常用于页面内跳转里，来显示跳转到的位置，使用举例
```HTML
<a href="#more_info">More Information</a>
```
位于页面其他地方、ID 为 more_info 的那个元素就是目标。该元素可能是这样的:
```HTML
<h2 id="more_info">This is the information you are looking for.</h2>
```
那么，如下 CSS 规则
```
#more_info:target {background:#eee;}
```
会在用户单击链接转向 ID 为 more_info 的元素时，为该元素添加浅灰色背景。

### 伪元素
伪元素用于创建一些不在文档树中的元素，并为其添加样式。
1. ::first-letter 伪元素 :
```HTML
p::first-letter {font-size:300%;}
```
可以得到段落首字符放大的效果。
2. ::first-line 伪元素
3. ::before 和::after 伪元素 :
```HTML
p.age::before {content:"Age: ";}
p.age::after {content:" years.";}
```
### 伪类与伪元素的区别
伪类的操作对象是文档树中已有的元素，而伪元素则创建了一个文档树外的元素。因此，伪类与伪元素的区别在于：有没有创建一个文档树之外的元素。

---

### 权重
浏览器通过权重来判断哪一些属性值与一个元素最为相关，从而在该元素上应用这些属性值。优先级是基于不同种类选择器组成的匹配规则。

#### 权重是如何计算的？

优先级就是分配给指定的CSS声明的一个权重，它由匹配的选择器中的每一种选择器类型的数值决定。

而当优先级与多个CSS声明中任意一个声明的优先级相等的时候，CSS中最后的那个声明将会被应用到元素上。

当同一个元素有多个声明的时候，优先级才会有意义。因为每一个直接作用于元素的CSS规则总是会接管/覆盖（take over）该元素从祖先元素继承而来的规则。

#### [MORE](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Specificity)

---

### 背景
- background-color
- background-image
- background-repeat   
- background-position   
- background-size
- background-attachment
- background(简写属性)

#### 背景图片
```css
p {
  background-image:url(images/turq_spiral.png);
}
```
#### 背景重复
repeat(默认),repeat-x,repeat-y,no-repeat.
#### 背景位置
```css
p#center {
  background-position:center;//top,left,bottom,right
}
```

#### 多背景图片
```css
p { background:  
    url(images/turq_spiral.png) 30px -10px no-repeat,
    url(images/pink_spiral.png) 145px 0px no-repeat,
    url(images/gray_spiral.png) 140px -30px no-repeat, #ffbd75;		
         }			 
```
#### 背景渐变
##### 线性渐变
```css
.gradient1 {
background:linear-gradient(#e86a43, #fff);//从上到下
}
.gradient3 {
    background:linear-gradient(-45deg, #e86a43, #fff);//从左上到右下
}      
.gradient2 { 1 background:linear-gradient(left, #64d1dd, #fff);
}

```
##### 渐变点
```css
.gradient1 {
  background:linear-gradient(#e86a43 20%, #fff 50%, #e86a43 80%); }
.gradient3 {     
 background:linear-gradient(#64d1dd, #fff 25%, #64d1dd 50%, #fff 75%, #64d1dd);
}
.gradient4 {
background:linear-gradient(#e86a43, #fff 25%, #64d1dd 25%, #64d1dd 75%, #fff 75%, #e86a43);
}
```

#### 放射性渐变
```css
.gradient1 {
background: -webkit-radial-gradient(#fff, #64d1dd, #70aa25);
  }
.gradient2 {
    background: -webkit-radial-gradient(circle, #fff, #64d1dd, #e86a43);
  }
.gradient3 {
    background: -webkit-radial-gradient(50px 30px, circle, #fff, #64d1dd,#4947ba);
  }
```
### 字体和文本

#### 字体族
```css
body {font-family: "Helvetica Neue",Helvetica,"PingFang SC","Hiragino Sans GB","Microsoft YaHei","微软雅黑",Arial,sans-serif;}
```
在指定文本的字体时，需要多列出几种后备字体， 以防第一种字体无效。这个字体的列表也叫字体栈。

#### 字体类

- serif，也就是衬线字体
- sans-serif，也就是无衬线字体
- monospace，也就是等宽字体
- cursive，也就是草书体或手写体
- fantasy，不能归入其他类别的字体(一般都是奇形怪状的字体)。
使用这些通用字体类的目的，就是确保在最坏的情况下，文档起码可以通过正确的字形来显示，将通用字体类写到最后即可。

#### 字体样式

值:italic、oblique、normal。

示例:h2 {font-style:italic;} 。

#### 文本属性

- 文本缩进: text-indent
- 字符间距: letter-spacing
- 单词间距: word-spacing
- 文本对齐: text-align，值:left、right、center、justify。
- 行高: line-height
- 文本装饰: text-decoration，值:underline、overline、line-through、none。

### 表单
```HTML
<form class='style_form' action='process_form.php' method='post'>
</form>
```
- action
  - 用于指定服务器上用来处理表单数据的文件的URL。
- method
  - (值要么是post，要么是get)用于指定怎么把数据发送到服务器上。
