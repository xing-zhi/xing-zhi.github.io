---
layout: post
title: "获取元素的位置和尺寸"
date:   2015-11-04 13:17:00
comments: true
categories: dom
---

说明：本文只涉及IE8+和标准模式。

文档被渲染后，文档中的每一个元素都对应一个矩形盒子，都有自己的位置和尺寸。本文讨论如何获取元素的位置和尺寸。

元素的位置包括两个不同坐标体系下的位置：相对于视口左上角的视口位置和相对于文档左上角的文档位置。

当文档没有滚动时，元素的视口位置和文档位置相同；当文档发生滚动时，元素的视口位置和文档位置通过页面滚动偏移量来转换。

因为视口位置往往在编程中更有用，所以获取元素的视口位置的方法已经被标准化，并且得到了所有浏览器的支持。在获取了元素的视口位置后，只需要获取页面滚动偏移量，就可以计算出元素的文档位置。

# 获取元素的尺寸和在视口中的位置
元素的`getBoundingClientRect`方法返回包含元素的尺寸（`width`和`height`）和在视口中的位置（`left`、`right`、`top`和`bottom`）的对象，但是IE8-返回的对象不包含`width`和`height`属性。

要在IE8-中使用`width`和`height`属性需要对这两个属性进行检查，同时提供后备。

{% highlight javascript linenos %}
const el = document.querySelector('.target'),
      box = el.getBoundingClientRect(),
      width = box.width || box.right - box.left,
      height = box.height  || box.bottom - box.top;
{% endhighlight %}

通过元素的`getBoundingClientRect`方法返回的是元素的`border-box`的尺寸和位置，即包括元素的内容区、内边距区和边框，不包括外边距区。

元素的`getBoundingClientRect`方法返回的是**元素在视口中当前位置的静态快照**，并非实时的。所以当文档有任何变化或者页面有滚动时，都需要重新获取。同时每次调用`getBoundingClientRect`方法时都会触发布局（`layout`/`reflow`），影响性能，注意不要在循环中调用`getBoundingClientRect`方法。

# 获取页面滚动偏移量和文档中的位置
在获取了元素在视口中的位置后，要获取元素在文档中的位置。只需要获取页面的滚动偏移量，就可以计算出元素的文档位置。

获取页面滚动偏移量的标准方法是查询`window.pageXOffset`和`window.pageYOffset`属性，这两个属性只存在于IE9+。同时，这两个属性是`window.scrollX`和`window.scrollY`的别名，但是`window.scrollX`和`window.scrollY`的只存在于IE Edge，为了更好的兼容性，使用`window.pageXOffset`和`window.pageYOffset`。

{% highlight javascript linenos %}
console.log(window.pageXOffset === window.scrollX);    // => true
console.log(window.pageYOffset === window.scrollY);    // => true
{% endhighlight  %}

在IE8-中，需要通过查询文档根节点（`document.documentElement`）的`scrollLeft`和`scrollTop`属性获取页面滚动偏移量。

兼容IE8的获取页面滚动偏移量的方法如下：
{% highlight javascript linenos %}
function getPageOffsets() {
  if ( window.pageXOffset ) {
    return {
      x: window.pageXOffset,
      y: window.pageYOffset
    };
  }

  const documentEl = document.documentElement;

  return {
    x: documentEl.scrollLeft,
    y: documentEl.scrollTop
  };
}
{% endhighlight  %}

在获取了元素的视口位置和页面滚动偏移量之后，只需要使用视口位置加上页面滚动偏移量就可以得到元素的文档位置。

{% highlight javascript linenos %}
const el = document.querySelector('#position-test'),
      pageOffset = getPageOffsets(),
      elBox = el.getBoundingClientRect(),
      documentTop = elBox.top + pageOffset.x,
      documentLeft = elBox.left + pageOffset.y;
{% endhighlight  %}

# 小结
通过元素的`getBoundingClientRect`方法可以获取元素的尺寸和在视口中的位置，通过`window.pageXOffset/scrollX`和`window.pageYOffset/scrollY`可以获取页面滚动偏移量，元素在视口中的位置加上页面滚动偏移量就得到了元素在文档中的位置。

元素对象还有包含元素的位置和尺寸信息的其他一些属性，这些属性对于在不支持`getBoundingClientRect`方法的老旧浏览器中用于确定元素的位置和尺寸很有用，但是在这些浏览器中大都有兼容性问题，这里不作讨论。

# 参考

[MDN Element.getBoundingClientRect()](https://developer.mozilla.org/en-US/docs/Web/API/Element/getBoundingClientRect)

[MDN Window.pageXOffset](https://developer.mozilla.org/en-US/docs/Web/API/Window/pageXOffset)

[MDN Window.scrollX](https://developer.mozilla.org/en-US/docs/Web/API/Window/scrollX)
