---
layout: post
title:  "图片懒加载"
date:   2015-08-28 16:05:00
comments: true
categories: performance
---

懒加载是指延迟资源的加载到真正需要该资源的时候，是一项常见的前端优化手段。使用了图片懒加载的网站的最典型代表就是[谷歌图片搜索](https://images.google.com/)和[百度图片搜索](http://image.baidu.com/)。

懒加载可以用于除图片之外的很多资源，这里仅以图片懒加载进行示例。

# 实现步骤
实现图片懒加载的基本思路如下：

1. 为需要进行懒加载的图片添加占位元素
2. 当某个事件（比如滚动）发生时，加载需要加载的图片

## HTML
首先我们需要为需要懒加载的图片添加占位元素，让占位元素充当图片在页面中渲染，从而可以确定当出发懒加载的事件发生时，是否加载该图片，同时避免了当图片加载后重绘页面。

下面是占位元素的一种实现，其中`width`和`height`属性是懒加载的图片最终在页面中的宽度和高度，实际生产代码中应该使用CSS设置；`data-src`属性值是图片实际的路径。

{% highlight html linenos %}
<img width="100" height="100" data-src="/images/lazy-loading.jpg" />
{% endhighlight %}

## 加载图片功能
当需要加载某张图片时，只需要设置该图片元素的`src`属性为`data-src`属性的值就可以加载图片了。同时为了避免后续重复加载这张图片，需要删除图片的`data-src`属性。

{% highlight javascript linenos %}
function loadImage (imgEl) {
  const src = imgEl.getAttribute('data-src');

  imgEl.src = src;
  imgEl.removeAttribute('data-src');
}
{% endhighlight %}

## 确定图片是否要加载
在加载图片之前，我们需要确定某张图片是否应该被加载，这里简单地以该图片是否在视口中来确定是否加载该图片。

{% highlight javascript linenos %}
function isToLoad(imgEl) {
  const rect = imgEl.getBoundingClientRect(),
        viewportHeight = window.innerHeight || document.documentElement.clientHeight;

  return (
    rect.left >= 0 &&
      (rect.top >= -rect.height && rect.top <= viewportHeight )
  );
}
{% endhighlight %}

## 处理事件，触发图片加载
当需要加载图片的事件发生时，判断需要加载的图片并且加载，这里以`scroll`和`touchmove`事件为例。

{% highlight javascript linenos %}
function lazyLoad(){
  const images = document.querySelectorAll('img[data-src]');

  [].forEach.call(images, function(image, index) {
    if ( isToLoad(image) ) {
      loadImage(image);
    }
  });
}

lazyLoad();
window.addEventListener('scroll',lazyLoad);
window.addEventListener('touchmove',lazyLoad);
{% endhighlight %}

# 优化
图片懒加载就基本实现了，但是还有两个问题。

一个问题是用户会看到图片的加载过程。如果预先加载一屏的图片，用户一般就不会看到图片的加载过程了（网络太差的情况下另说），用户体验会好很多。

另一个问题是如果在页面中部刷新页面时，用户的滚动方向是不确定的，所以相对于当前视口上一屏的图片也需要预先加载。

下面就来修改一下确定一张图片是否应该加载的函数，使当前视口的上下一屏中的图片都会被预加载。

{% highlight javascript linenos %}
function isToLoad(imgEl) {
  const rect = imgEl.getBoundingClientRect(),
        viewportHeight = window.innerHeight || document.documentElement.clientHeight;

  return (
    rect.left >= 0 &&
      (rect.top >= -viewportHeight - rect.height && rect.top <= viewportHeight * 2 )
  );
}
{% endhighlight %}

# 完整示例
下面是一个完整的示例。

<p data-height="266" data-theme-id="18224" data-slug-hash="NGKywj" data-default-tab="result" data-user="xingzhi" class='codepen'>See the Pen <a href='http://codepen.io/xingzhi/pen/NGKywj/'>image lazy loading</a> by xingzhi (<a href='http://codepen.io/xingzhi'>@xingzhi</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
