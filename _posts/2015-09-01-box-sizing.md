---
layout: post
title:  "使用「box-sizing」简化布局时的计算"
date:   2015-09-01 11:13:00
comments: true
categories: css
---

`box-sizing`属性用于修改盒模型的`width`属性和`height`属性的意义，该属性一共有3个值

+ `content-box`：`width`和`height`属性对应内容区的宽度和高度。这是默认的渲染方式。
+ `border-box`：`width`和`height`属性对应内容区加上内边距区和边框的宽度和高度。这是老版本IE在怪异模式下的渲染方式。
+ inherit：继承

下面是设置相同的`width`、`padding`、`border-width`属性，不同的`box-sizing`属性时渲染差异的示例。

<p data-height="270" data-theme-id="18224" data-slug-hash="VvwWJW" data-default-tab="result" data-user="xingzhi" class='codepen'>See the Pen <a href='http://codepen.io/xingzhi/pen/VvwWJW/'>box-sizing example</a> by xingzhi (<a href='http://codepen.io/xingzhi'>@xingzhi</a>) on <a href='http://codepen.io'>CodePen</a>.</p>

# 丢弃繁琐的计算
对于一个块级框，可视的渲染区域是内容区、内边距区和边框。

当`box-sizing`为`content-box`时，`width`的值需要通过渲染区域宽度的值减去内边距和边框的宽度计算得到，当`padding`或者/和`border-width`改变时还需要重新计算并且设置`width`的值。

当`box-sizing`为`border-box`时，`width`值就是渲染区域宽度的值，我们不需要计算内容区的宽度，而且当`padding`或者/和`border-width`改变时，也不需要计算内容区的宽度。

没有了这些繁琐的计算，阳光都更灿烂了有木有。

# 设置`box-sizing`的正确姿势
要使用`border-box`进行布局，首先要重置所有元素的`box-sizing`属性，我们第一时间想到的就是使用全局选择器将所有元素的`box-sizing`属性都设置为`border-box`，但是更好的方式是重置html元素的`box-sizing`属性，而其他属性使用继承机制。

{% highlight css linenos %}
html {
  box-sizing: border-box;
}
*, *:before, *:after {
  box-sizing: inherit;
}
{% endhighlight %}

这种设置方法的一个优势在于，当页面中的某一个部分需要使用`content-box`进行布局时，只需要在最外层元素上设置`box-sizing`属性就可以了。

# 兼容性
`box-sizing`属性的兼容性见下图：

![box-sizing的兼容性](/images/caniuse-box-sizing.png)

只要不支持IE8以下的浏览器，那就使用把`box-sizing`设置为`border-box`吧，让自己的生活更好一些。
