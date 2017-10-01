---
title: 阅读Android原生计算器源码
date: 2017-05-17 09:36:56
tags: Android
categories: 移动开发
---
# Read Android Original Calculator

# 项目结构
- Calculator
  - CalculatorPadLayout
    - CalculatorNumericPadLayout
  - CalculatorPadViewPager
- CalculatorExpressionBuilder
- CalculatorExpressionEvaluator
- CalculatorExpressionTokenizer

<!-- more -->

## Calculator
##### onBackPressed()
处在第高级操作符页面时，点击back键，返回初始页面。
##### onUserInteraction()
Called whenever a key, touch, or trackball event is dispatched to the activity.

在处理用户交互前，如果由正在进行的动画请立即终止。
##### onEquals()
至于计算，只要获得式子的字符串，然后交给 org.javia.arity.Symbol就行了。
```java
//一句话的事
double result = mSymbols.eval(expr);
```

## CalculatorPadViewPager
##### 为什么在显示page1的时候page2会显示出来一部分呢（右侧青色那一条）？
答案在setPageMargin(-24);由于是负数，这样每个page之间就会有重叠，就出现了明明显示的第一个view，结果在右侧显示了一部分第二个view的layout。
##### 为什么向左滑动的时候page1的位置一直保持不动？不应该是向左侧滑出吗？
看setPageTransformer(false, mPageTransformer);

```java
/**
* Apply a property transformation to the given page.
*
* @param page Apply the transformation to this page
* @param position Position of page relative to the current front-and-center
*                 position of the pager. 0 is front and center. 1 is one full
*                 page position to the right, and -1 is one page position to the left.
*/
public void transformPage(View page, float position);
```

看一下源码里的方法说明，这里的position其实是相对的，并不是常规理解的位置下标，它是以当前屏幕的正中心为坐标原点，当page的中心和屏幕的原点重合（即page正在完全显示时）position=0;
当page向左侧滑动，慢慢淡出屏幕时，该过程中page的中心相对屏幕的中心原点在沿着X轴向左移动，此时positon < 0，当page完全滑出屏幕时，positon = -1;
同理，当page向右滑动，慢慢淡出屏幕时，该过程中page的中心相对屏幕的中心原点在沿着X轴向右移动，此时positon > 0，当page完全滑出屏幕时，positon = 1；

有了上面的分析，其实可以把positon看成正在滑动的page滑出屏幕的比例，正负代表往哪个方向滑动。所以，要想在滑出数学计算公式的page过程中保持当前输入的page位置不动，根据滑出屏幕的比例设置该page在X轴的偏移量就可以了，view.setTranslationX(getWidth() * -position); 同理，把数学计算公式的page滑出时取消这个偏移量，view.setTranslationX(0); 到这里疑问2也解决了。

更多的ViewPager切换动画请参考开源项目JazzyViewPager
##### 还有就是当page2滑出后宽度为什么没有铺满、左边还要留出一部分透明区域呢？
原来是复写了PagerAdapter的getPageWidth方法，看一下该方法的说明：

```java
/**
 * Returns the proportional width of a given page as a percentage of the
 * ViewPager's measured width from (0.f-1.f]
 *
 * @param position The position of the page requested
 * @return Proportional width for the given page position
 */
public float getPageWidth(int position) {
    return 1.f;
}
```
说的很清晰了，根据position设置当前页面宽度的百分比。

```java
public float getPageWidth(int position) {
            return position == 1 ? 7.0f / 9.0f : 1.0f;
        }
```

##### OnPageChangeListener解读
Callback interface for responding to changing state of the selected page.

###### OnPageChangeListener这个接口需要实现三个方法：（onPageScrollStateChanged，onPageScrolled ，onPageSelected）：

onPageScrollStateChanged(int arg0)   ，此方法是在状态改变的时候调用，其中arg0这个参数。
有三种状态（0，1，2）。arg0 ==1的时辰默示正在滑动，arg0==2的时辰默示滑动完毕了，arg0==0的时辰默示什么都没做。

onPageScrolled(int arg0,float arg1,int arg2)    ，当页面在滑动的时候会调用此方法，在滑动被停止之前，此方法回一直得到调用。其中三个参数的含义分别为：
- arg0 :当前页面，及你点击滑动的页面
- arg1:当前页面偏移的百分比
- arg2:当前页面偏移的像素位置

onPageSelected(int arg0) ：   此方法是页面跳转完后得到调用，arg0是你当前选中的页面的Position（位置编号）。

## CalculatorPadLayout
自定义了数字键盘的布局。规定了每个按键的长宽高，以及位置。
## CalculatorNumericPadLayout extends CalculatorPadLayout
根据id设置按钮属性。

## 总结
原本不知道怎么实现的效果，发现方法其实很简单。当然，你得对源码很了解，才能想到。

## 小要点
#### SpannableStringBuilder
This is the class for text whose content and markup can both be changed

#### TextWatch
When an object of a type is attached to an Editable, its methods will be called when the text is changed.
