---
title: CSS的重要性
date: 2017-07-23 08:55:30
tags: css基础
categories: CSS
---
# CSS

##  玉伯看HTML和CSS代码的背后
[https://github.com/lifesinger/blog/issues/105](https://github.com/lifesinger/blog/issues/105)
>
> 之前在freeCodeCamp上看到两篇置顶的前端入门文章。第一篇的内容涉及html和css。第二篇就讲javascritpt。看第一篇内容时，真是被文章丰富的内容吓了一跳。当时心里就嘀咕，真的要学这么多吗？
>
> 直到今天看了玉伯的文章，才意识到html和css同样非常重要。所以，特地做一下笔记，把所思所想理一下。

<!-- more -->

让块元素水平居中。一般大家都会写成：
```CSS
.content {
    width: 980px;
    margin: 0 auto;
}
```
上面的代码能正常工作，大部分情况下也不会有问题，但上面的代码存在思维的懒惰。应该写成:
```CSS
.content {
    width: 980px;
    margin-left: auto;
    margin-right: auto;
}
```
看起来代码变多了，变啰嗦了。但如果你真的了解 **margin: 0 auto** 的话，就能明白其中的意义。

**margin: 0 auto** 的实现：
```CSS
{
	margin-top:0;
	margin-bottom:0;
	margin-left:auto;
	margin-right:auto;
}
```
可以看到 **margin: 0 auto** 中会帮你把top和bottom重置了。可是，你只想要“水平居中”而已。

这种不纯粹会导致顺序和优先级的依赖，比如有另一处要给 margin-top/bottom 赋值时，就必须要提高优先级。

而且 **margin: 0 auto** 中的 0 绝大部分情况下是冗余的，页面上早就有 **reset.css** 或 **normalize.css** 重置过。

### 最小影响原则

你在写某段CSS代码时，首先要非常清楚地知道这段CSS代码的功能，其次要尽量严格保障这段CSS代码只实现了你想要实现的功能。

这就如医生动手术，好好做好本分就行，千万别留下一个小镊子在病人身体里。

与HTML代码一样，对CSS代码来说，很重要的两个衡量标准也是稳定和灵活。
