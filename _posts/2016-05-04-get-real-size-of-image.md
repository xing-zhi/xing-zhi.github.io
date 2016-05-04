---
layout: post
title:  "获取图片的实际尺寸"
date:   2016-05-04 11:50:00
comments: true
categories: front-end
---

有时候我们会遇到需要获取图片的实际尺寸的情形，而图片的`width`和`height`属性并代表图片的实际尺寸。

还好现代浏览器提供了在`<img>`元素上提供了`naturalWidth`和`naturalHeight`两个属性，用于获取图片的实际尺寸。

{% highlight javascript linenos %}
document.addEventListener('DOMContentLoaded', function() {
  const image = document.querySelector('img');

  console.log(image.naturalWidth);    // => 图片的实际宽度
  console.log(image.naturalHeight);    // => 图片的实际高度
});
{% endhighlight %}

# 兼容性
这两个属性兼容性良好，包括IE9的现代浏览器都支持。可参看[MDN上关于这两个属性的兼容性说明](https://developer.mozilla.org/en-US/docs/Web/API/HTMLImageElement#Browser_compatibility)

# 参考
[MDN HTMLImageElement](https://developer.mozilla.org/en-US/docs/Web/API/HTMLImageElement)
