---
layout: post
title:  "不折行的长下拉菜单项及背后的原理"
date:   2015-08-26 17:20:00
comments: true
categories: css
---

把多级导航做成下拉菜单的形式是现在的通用设计。

仅使用CSS实现一个下拉菜单是很简单的，主要思路就是使用绝对定位。

但是在下拉菜单项的内容较长，准确地说是长度超过上一级菜单项的内容长度时，下拉菜单项会折行。原本整齐漂亮的下拉菜单一下子就变丑了。

![长下拉菜单项折行](/images/dropdown-menu-item-wrap.png)

要内容长的下拉菜单不折行，实现起来也很简单，只需要设置下拉部分的`white-space`属性为`nowrap`或者`pre`即可。

说得有些抽象，下面是示例。

<p data-height="266" data-theme-id="18224" data-slug-hash="jPgRYq" data-default-tab="result" data-user="xingzhi" class='codepen'>See the Pen <a href='http://codepen.io/xingzhi/pen/jPgRYq/'>不折行的长下拉菜单项</a> by xingzhi (<a href='http://codepen.io/xingzhi'>@xingzhi</a>) on <a href='http://codepen.io'>CodePen</a>.</p>

实现方法也说了，示例也看了，如果只想知道如何实现，看到这儿就不需要继续往下看了。

而如果心里还有两个疑问：为什么会折行？为什么设置了`white-space`后又不折行了？那么就继续往下看吧。

首先来一点理论。

# 理论
在CSS的基本盒模型中，水平方向的长度一共涉及有7个属性：`margin-left`、`border-left`、`padding-left`、`width`、`padding-right`、`border-right`和`margin-right`。其中有3个属性的值可以设置为`auto`：`margin-left`、`width`和`margin-right`。除了`width`的默认值是`auto`外，其他属性的默认值都是`0`。

在渲染过程中，这7个属性的值需要满足一个条件

    margin-left + border-left + padding-left + width + padding-right + border-right + margin-right = 元素的包含块的宽度

当这个等式不成立时，`margin-right`会被重置为`auto`，然后自动调整从而使等式成立。

如何确定元素的包含块可以看[CSS包含块](css-containing-block.html)。

理论到此为止，下面来说一说上面的两个疑问。

# 为什么会折行
折行的原因很简单：下拉菜单项的宽度超过了其包含块的宽度，内容（`width`的值为`auto`）被自动调整从而使上面的等式成立。

在上面的示例中，下拉部分是通过绝对定位实现的，它的包含块就是上一级菜单的内边距块。同时，`width`属性的值为`auto`，渲染时`width`属性就会自动调整以满足上面的等式，当内容长度超过`width`属性的值时就会折行。

# 为什么又不折行了
理解了折行的原因，不折行的原因就简单多了。

通过设置下拉菜单项内容不可以折行，`width`属性的值被自动调整为在一行里包含所有内容，进一步引发`margin-right`被重置为`auto`，然后自动调整以使上面的等式成立。
