---
layout: post
title:  "JavaScript异步编程之分割耗时任务"
date:   2015-08-29 12:10:00
comments: true
categories: javascript async
---

因为JavaScript是单线程的，任何耗时的任务都将阻塞线程，使程序失去响应。

下面有一个示例，其中使用一个运行`1e9`次的`while`循环模拟耗时任务。

{% highlight javascript linenos %}
function longRunning() {
 let counter = 0;
 const times = 1e9;

 while ( counter++ < times )
   ;
}
{% endhighlight %}

示例的页面有一个按钮，按钮的事件处理程序中会把事件处理程序运行的开始时间添加到页面中，然后运行这个耗时任务，然后把结束时间添加到页面中。

<p data-height="266" data-theme-id="18224" data-slug-hash="gaYZGq" data-default-tab="result" data-user="xingzhi" class='codepen'>See the Pen <a href='http://codepen.io/xingzhi/pen/gaYZGq/'>long-running task blocks</a> by xingzhi (<a href='http://codepen.io/xingzhi'>@xingzhi</a>) on <a href='http://codepen.io'>CodePen</a>.</p>

当点击按钮后，页面会失去响应。同时我们会看到开始时间和结束时间是同时添加到页面中的，这是因为**对于任何DOM操作，页面的渲染是异步的**。

下面就对耗时任务进行分割，分割后的任务每次执行`1e7`次循环。而且在任务结束后把任务结束运行的时间添加到页面中。

{% highlight javascript linenos %}
function longRunning() {
  let counter = 0;
  const times = 1e9,
        timesPerTick = 1e7;

  function tick() {
    let n = 0;
    while ( n < timesPerTick ) {
      n++;
      counter++;
    }
  }

  const timer = setInterval(function() {
    tick();
    if ( counter >= times ) {
      const taskFinishTime = document.createElement('p');

      taskFinishTime.innerHTML = '任务运行结束时间： ' + new Date();
      logContainer.appendChild(taskFinishTime);

      clearInterval(timer);
    }
  }, 0);
}
{% endhighlight %}

下面是对耗时任务进行分割后的示例。

<p data-height="266" data-theme-id="18224" data-slug-hash="epObjv" data-default-tab="result" data-user="xingzhi" class='codepen'>See the Pen <a href='http://codepen.io/xingzhi/pen/epObjv/'>splitting long-running task</a> by xingzhi (<a href='http://codepen.io/xingzhi'>@xingzhi</a>) on <a href='http://codepen.io'>CodePen</a>.</p>

这时我们点击按钮时，页面会立即更新，事件处理程序的开始运行时间和结束运行时间被立即添加到页面中。而任务还在继续运行，直到运行结束，任务结束运行的时间才会被添加到页面中。

在浏览器中，一般使用`setTimeout`或者`setInterval`分割耗时任务。而Node中使用`setImmediate`更合适。
