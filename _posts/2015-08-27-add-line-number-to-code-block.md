---
layout: post
title:  "为代码块添加行号以增加可读性"
date:   2015-08-27 15:41:00
comments: true
categories: css
---

今天给博客的代码块添加了行号，这对于`jekyll`来说很简单，只需要在指定代码语法高亮时添加`linenos`属性就好了。

但是如何使用CSS来实现呢？

因为整个代码块只是一行一行的文本，所以我们是无法使用CSS针对每一行代码添加样式的。

所以首先我们需要使用JavaScript把代码块中的每一行包裹在一个元素中，下面的代码将每一行代码包裹在一个`span`元素中，并且添加了类`code-line`。
  
{% highlight javascript linenos %}
(function() {
  'use strict';

  const codeBlocks = document.querySelectorAll('pre.has-line-num code');

  Array.prototype.forEach.call(codeBlocks, (codeBlock) => {
    codeBlock.innerHTML = codeBlock.textContent
       .trim()
      .replace(/^/, '<span class="code-line">')
      .replace(/\n/g, '</span>\n<span class="code-line">')
      .replace(/$/, '</span>');
  });
})();
{% endhighlight %}

现在就可以针对每一行代码添加样式了。

要添加行号，我们需要使用`counter-reset`和`counter-increment`两个CSS属性，而且它们的浏览器支持还不错。（因为它们是在CSS2.0规范中添加的。）

![counter-reset和counter-increment的浏览器兼容性](/images/caniuse-counter-reset.png)

`counter-reset`属性用于初始化一个计数器，接受一个计数器名和可选的计数器初始值作为值。默认的计数器初始值为1。

`counter-increment`属性用于改变一个计数器，接受一个计数器名和可选的增量作为值。默认的增量为1。

关于`counter-reset`和`counter-increment`的更多内容请参考MDN：[counter-reset](https://developer.mozilla.org/en-US/docs/Web/CSS/counter-reset)和[counter-increment](https://developer.mozilla.org/en-US/docs/Web/CSS/counter-increment)。

现在，就来给代码块添加上行号。
{% highlight css linenos %}
pre code {
  counter-reset: code-line-num;    /* 初始化一个叫code-line-num的计数器 */
}

pre code .code-line:before {
  content: counter(code-line-num);    /* before伪元素以计数器的值为内容 */
  counter-increment: code-line-num;    /* 将伪元素的值增加1 */
}
{% endhighlight %}

这时，我们再添加上其他样式就可以了，下面是一个示例。

<p data-height="266" data-theme-id="18224" data-slug-hash="MagyqB" data-default-tab="result" data-user="xingzhi" class='codepen'>See the Pen <a href='http://codepen.io/xingzhi/pen/MagyqB/'>给代码块添加行号</a> by xingzhi (<a href='http://codepen.io/xingzhi'>@xingzhi</a>) on <a href='http://codepen.io'>CodePen</a>.</p>




