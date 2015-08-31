---
layout: post
title:  "JavaScript异步编程之使用「web worker」运行耗时任务"
date:   2015-08-31 10:30:00
comments: true
categories: javascript async
---

在[JavaScript异步编程之分割耗时任务](../29/splitting-long-running-task.html)中，我们通过定时器把耗时任务分割成多个部分，从而避免了耗时任务阻塞线程，进而阻塞UI。

好在HTML5为我们提供了「web worker」，使得我们可以在另一个线程中运行JavaScript。使用「web worker」运行耗时任务比使用定时器要高效优雅地多。

从下图可以看出，「web worker」的兼容性还是很好的，只是IE又拖后腿了...

![web worker兼容性](/images/caniuse-web-worker.png)

为了兼容不支持的浏览器，我们需要为不支持「web worker」的浏览器提供后备方案：使用定时器分割耗时任务。
{% highlight javascript linenos %}
if ( Worker ) {
  // 使用web worker
} else {
  // 使用定时器
}
{% endhighlight %}

# 创建worker
一个worker实例通过给全局构造函数`Worker`传递需要使用worker线程中运行的脚本的URL创建。传入的URL需要满足同源策略。

{% highlight javascript linenos %}
const worker = new Worker('worker.js');
{% endhighlight %}

# 受限的worker
worker线程在很多方面受限。

worker线程中的脚本无法获取和修改DOM，这避免了主线程和worker线程在修改DOM上产生竞态。

worker线程中的脚本无法获取window全局变量，而是拥有自己的全局变量`self`，在它的上面绑定了一些标准JavaScript对象和方法，如`setTimeout`、`Math`、`XMLHttpRequest`（不过`responseXML`和`channel`属性始终是`null`）和`WebSocket`（如果浏览器支持的话）等。

worker线程和主线程完全处于两个独立的作用域中，主线程和worker线程之间只能通过消息交换数据，而且数据是以**复制**的形式而不是赋值的形式传递。

# worker线程和主线程之间的消息传递
主线程和worker线程之间通过消息机制交换数据：传递数据使用`postMessage`方法，获取数据监听`message`事件。

{% highlight javascript linenos %}
// main.js
const worker = new Worker('worker.js');

worker.postMessage('foo');

worker.addEventListener('message', function(e) {
  // e.data的值就是传递的数据
});
{% endhighlight %}

{% highlight javascript linenos %}
// worker.js
self.addEventListener('message', function(e) {
  // e.data的值就是传递的数据
});

self.postMessage('bar');
{% endhighlight %}

`message`事件对象的`data`属性的值就是传递的数据。

主线程和worker线程之间数据的传递使用复制的方式，这避免了对传递的数据的修改对原线程的值产生的影响。

# 示例
下面的示例把[JavaScript异步编程之分割耗时任务](../29/splitting-long-running-task.html)中的耗时任务使用「web worker」运行。

<p data-height="266" data-theme-id="18224" data-slug-hash="WQNvXp" data-default-tab="result" data-user="xingzhi" class='codepen'>See the Pen <a href='http://codepen.io/xingzhi/pen/WQNvXp/'>run long-running task with web worker</a> by xingzhi (<a href='http://codepen.io/xingzhi'>@xingzhi</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
