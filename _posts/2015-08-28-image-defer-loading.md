---
layout: post
title:  "图片延迟加载"
date:   2015-08-28 18:27:00
comments: true
categories: performance
---

在[图片懒加载](./image-lazy-loading.html)中简单实现了图片懒加载。

对于页面变化是连续的或者说可预测的时候，比如页面滚动，使用懒加载是不错的选择。

可是懒加载技术是有局限的，比如当页面变化是不可预测的时候，懒加载就不适合了。

比如一个SPA由3个页面构成，每个页面都有一些图片，当首页加载后，用户会进入另外两个页面中的哪一个是不确定的，此时最好的方式不是使用懒加载，而是使用延迟加载。

延迟加载的基本思路是：首先加载呈现给用户的页面，而未呈现给用户的页面中的一些资源，比如图片，暂时不加载，从而获取更快的响应；当网络空闲时立即加载延迟加载的内容。

由上面的分析也可以看出，采取什么策略加载页面和页面的结构是密切相关的，在设计的过程中需要综合考虑。

延迟加载也可以用于除图片之外的其他资源，这里还是以图片延迟加载进行示例。

# 实现步骤
由上面的分析，延迟加载主要分为两部分：

1. 为需要进行延迟加载的图片添加占位元素
2. 当网络空闲时加载被延迟加载的图片

## HTML
这一部分和[图片懒加载](./image-lazy-loading.html)相同，就不再重复，具体可以参考[图片懒加载](./image-lazy-loading.html)。

## 延迟加载图片
`window.onload`事件在页面加载的最后触发，此时页面中所有的需要被加载的内容都已经被加载了，对于该网页而言，就可以认为网络已经空闲了，可以加载被延迟加载的图片了。

{% highlight javascript linenos %}
function deferLoad() {
  const images = document.querySelectorAll('img[data-src]');

  [].forEach.call(images, function(image) {
    const src = image.getAttribute('data-src');

    image.setAttribute('src', src);
    image.removeAttribute('data-src');
  });
}

window.onload = deferLoad;
{% endhighlight %}

# 完整示例
下面是一个简单的示例。

<p data-height="266" data-theme-id="18224" data-slug-hash="zvOWYQ" data-default-tab="result" data-user="xingzhi" class='codepen'>See the Pen <a href='http://codepen.io/xingzhi/pen/zvOWYQ/'>image defer loading</a> by xingzhi (<a href='http://codepen.io/xingzhi'>@xingzhi</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
