---
layout: post
title: "轮播中实现根据点击位置进行翻页"
date: 2016-05-12 20:30:00
comments: true
categories: front-end
---

这里的方法可以用于任何需要翻页的地方，只是以熟悉的图片轮播来说明。同时这并不是讲解轮播的，所有不会涉及如何实现轮播的细节。

常用的图片轮播是实现两个翻页按钮，点击一个向前翻页，点击另一个时向后翻页。这种方法的一个不足之处是用户需要把鼠标移动到翻页按钮所在的位置才能进行翻页。

另一种实现方式就是当在大图区域的左侧点击时就向前翻页，在右侧点击时就向后翻页。这需要我们对点击时鼠标的位置进行判断。

{% highlight javascript linenos %}
const carousel = document.querySelector('#carousel');

carousel.addEventListener('click', function(e) {
  const pointX = e.screenX;
  const box = this.getBoundingClientRect();
  const middleX = (box.left + box.right) / 2;

  if ( pointX > middleX ) {
    // 向后翻页
  } else {
    // 向前翻页
  }
});
{% endhighlight %}

同时，我们需要告诉用户点击图片的不同位置可以进行翻页，这需要使用个性化的鼠标指针光标。个性化的光标可以通过给`cursor`设置图片来实现。

{% highlight css linenos %}
#carousel.left-arrow {
    cursor: url('/img/left-arrow.png') default;
}
#carousel.right-arrow {
    cursor: url('/img/right-arrow.png') default;
}
{% endhighlight %}

需要指出的是，设置`cursor`属性时，一定要带上一个指定值，否则无效。

在这之后就需要通过监听`mousemove`事件给轮播图片添加不同的类来使用不同的光标。

{% highlight javascript linenos %}
const carousel = document.querySelector('#carousel');

carousel.addEventListener('mousemove', function(e) {
  const pointX = e.screenX;
  const box = this.getBoundingClientRect();
  const middleX = (box.left + box.right) / 2;

  if ( pointX > middleX ) {
    this.classList.remove('right-arrow');
    this.classList.add('left-arrow);
  } else {
    this.classList.remove('left-arrow');
    this.classList.add('right-arrow');
  }
});
{% endhighlight %}

因为点击进行翻页时和显示个性化光标时都需要判断光标在图片水平方向上的位置，所以可以把这个判断抽象为一个函数，或者只在`mousemove`事件处理函数中进行判断，并记录方向，在`click`事件处理函数中根据这个方向进行翻页。
