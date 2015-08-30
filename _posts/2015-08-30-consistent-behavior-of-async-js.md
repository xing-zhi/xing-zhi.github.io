---
layout: post
title:  "JavaScript异步编程之行为一致性"
date:   2015-08-30 10:50:00
comments: true
categories: javascript async
---

当看到标题的时候，你心里一定会有疑问：行为一致性？什么行为？

行为在这里指的是函数的行为：同步执行还是异步执行。

行为一致性指的是确保我们写的函数始终同步执行或者异步执行，而不是有时候同步执行，有时候异步执行。

因为异步函数都是建立在其他异步的基础之上的，而异步函数的结果也是以异步的形式提供的，所以行为不一致的函数会引发混乱，引入bug，而且给使用的人带来困惑。

# 行为不一致的函数会引入bug

下面来看一个因为函数行为不一致而引发的bug的示例。

<p data-height="266" data-theme-id="18224" data-slug-hash="zvOVPO" data-default-tab="result" data-user="xingzhi" class='codepen'>See the Pen <a href='http://codepen.io/xingzhi/pen/zvOVPO/'>sometime async function causes bug</a> by xingzhi (<a href='http://codepen.io/xingzhi'>@xingzhi</a>) on <a href='http://codepen.io'>CodePen</a>.</p>

示例中的`ajaxGet`函数对获取的数据进行了缓存。当每次调用时，如果数据已经缓存，直接使用缓存的数据，否则使用`Ajax`异步获取。

{% highlight javascript linenos %}
const ajaxGet = (function() {
  const cache = {};

  return function(url, cb) {
    if ( cache[url] ) {
      cb(cache[url]);
    } else {
      const xhr = new XMLHttpRequest();

      xhr.open('GET', url);
      xhr.send();

      xhr.onload = function() {
        cache[url] = this.responseText;
        cb(this.responseText);
      };
    }
  };
})();
{% endhighlight %}

在使用`ajaxGet`时是假定它始终是异步的，在调用它之后，立即更新了UI，提示用户正在获取数据；当数据成功获取后，再次更新UI，显示获取的数据。

当没有缓存时，先提示用户正在获取数据，然后显示获取的数据。而当有缓存时，会先显示获取的数据，然后提示用户正在获取数据，但是数据已经被这个提示替换掉了。

# 行为一致的函数
行为不一致是因为函数有多个分支，其中一些分支是同步的，比如上面的示例中，根据数据是否已经缓存分成了两个分支：当有缓存时直接使用数据（同步），否则异步获取（异步）。

而要维持函数的行为一致只需要以异步的形式提供同步分支的结果。

下面是改写后的函数，改动只有一处：当有缓存数据时，使用`setTimeout`把之前的同步执行的函数包裹起来，使它异步执行。

{% highlight javascript linenos %}
const ajaxGet = (function() {
  const cache = {};

  return function(url, cb) {
    if ( cache[url] ) {
      setTimeout(function() {
        cb(cache[url]);
      }, 0);
    } else {
      const xhr = new XMLHttpRequest();

      xhr.open('GET', url);
      xhr.send();

      xhr.onload = function() {
        cache[url] = this.responseText;
        cb(this.responseText);
      };
    }
  };
})();
{% endhighlight %}

下面是改写后的示例。

<p data-height="266" data-theme-id="18224" data-slug-hash="KdPjRJ" data-default-tab="result" data-user="xingzhi" class='codepen'>See the Pen <a href='http://codepen.io/xingzhi/pen/KdPjRJ/'>always async function</a> by xingzhi (<a href='http://codepen.io/xingzhi'>@xingzhi</a>) on <a href='http://codepen.io'>CodePen</a>.</p>

在浏览器端使用`setTimeout`把同步行为转化为异步行为，而在Node中使用`process.nextTick`。
