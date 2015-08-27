---
layout: post
title:  "在下拉选项末尾添加小三角来标记下拉选项有下拉列表"
date:   2015-08-27 10:20:00
comments: true
categories: css
---

在实现下拉菜单时候，会遇到并不是所有的选项都有下拉列表的情况，通常的设计是在有下拉列表的选项后面添加一个向下的小三角。

更进一步的是在下拉列表展开时，小三角指向上方；下拉列表隐藏时，小三角又恢复为指向下方；而且变化过程以动画形式实现。

下面就来一步一步地实现动画小三角。

首先来做一个小三角。

# 通过设置`border`属性实现小三角
当我们为一个元素的`border`的每一边设置不同的颜色，我们就能观察到元素的每一边的边框实际上是梯形。那我们把元素的`width`属性和`height`属性都设置为`0`时，我们就得到了三角形。

还是来看示例吧。

<p data-height="266" data-theme-id="18224" data-slug-hash="QjLydw" data-default-tab="result" data-user="xingzhi" class='codepen'>See the Pen <a href='http://codepen.io/xingzhi/pen/QjLydw/'>使用边框实现三角</a> by xingzhi (<a href='http://codepen.io/xingzhi'>@xingzhi</a>) on <a href='http://codepen.io'>CodePen</a>.</p>

做好了小三角，下面我们来把它添加到有下拉列表的下拉选项中。

# 通过`after`伪元素添加小三角
要把小三角添加到下拉选项后面并不需要添加一个新的元素，利用`after`伪元素就可以了。

{% highlight css linenos %}
.dropdown {
    &:after {
        content: '';
        border: 5px solid transparent;
        border-top-color: black;
        position: absolute;
        top: 50%;
        right: 0;
        transition: transform 0.5s;
        transform-origin: 50% 25%;
        transform: translate(0, -25%);
    }
    &:hover {
        &:after {
            transform: translate(0, -25%) rotate(-180deg);
        }
    }
}
{% endhighlight %}

这里需要注意的是，因为`transform`属性默认的变换参考点是元素的中心，而我们的小三角只是元素的四分之一，所以要使两种状态下的小三角位置不变，需要设置`transform-origin: 50% 25%`；要使小三角垂直方向居中，需要设置`transform: translate(0, -25%)`。

把小三角添加到下拉选项后面之后，下一步就是当下拉列表展开或者隐藏时，小三角以动画的形式实现切换。

# 动起来
我们已经为小三角定义了两种状态：下拉列表隐藏时小三角向下，表示有下拉列表；下拉列表显示时小三角向上，表示下拉列表能够隐藏。

要两个状态的切换以动画的形式实现，只需要把`transform`属性添加给`transition`属性。改动很小，这里就不再贴代码了，直接在完整示例中看吧。

# 完整示例

下面是完整的示例。

<p data-height="266" data-theme-id="18224" data-slug-hash="bVbEaG" data-default-tab="result" data-user="xingzhi" class='codepen'>See the Pen <a href='http://codepen.io/xingzhi/pen/bVbEaG/'>带小三角的下拉菜单</a> by xingzhi (<a href='http://codepen.io/xingzhi'>@xingzhi</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
