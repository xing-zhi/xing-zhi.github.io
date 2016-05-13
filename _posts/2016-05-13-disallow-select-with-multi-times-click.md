---
layout: post
title: "禁用双击选中"
date: 2016-05-13 18:30:00
comments: true
categories: css
---

通过双击鼠标选中内容是一个很常用、很便捷的操作，但是有时候我们需要用户多次点击某个区域，但是并不希望出现这个区域或者这个区域的某一部分被选中的情形。

要实现这个功能就需要用到`user-select`这个CSS属性了。因为这个属性属于CSS4，所有兼容性并不是很好，但是我们需要使用的`use-select: none`，包括IE10+的现代浏览器都支持。只是需要添加浏览器前缀。

![user-select的兼容性](/images/caniuse-user-select.png)

而如果需要支持IE9和IE8，可以使用`selectstart`事件。

{% highlight html linenos %}
<div class="unselectable">不能通过双击选中</div>
{% endhighlight %}

{% highlight css linenos %}
.unselectable {
    -webkit-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
}
{% endhighlight %}

{% highlight javascript linenos %}
document.querySelectorAll('.unselectable').addEventlistener('selectstart', function() {
  return false;
});
{% endhighlight %}

当我们需要切换禁用/启用双击选中某个区域时，只需要在这个区域的最外层元素上添加/删除`unselectable`类就好了。

# 参考
[MDN user-select](https://developer.mozilla.org/en-US/docs/Web/CSS/user-select)

[Disable/enable text selection in IE](http://stackoverflow.com/questions/14556979/disable-enable-text-selection-in-ie)
