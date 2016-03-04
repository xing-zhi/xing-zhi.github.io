---
layout: post
title:  "让file类型的input每次选择文件时都触发「change」事件"
date:   2016-03-02 20:25:00
comments: true
categories: front-end
---

使用`type`为`file`的`input`可以方便地实现文件上传功能。

在使用这个`input`时通常是在它的`change`事件处理器中执行必要的逻辑。

而`change`事件只有在内容发生变化的时候才会触发，这就意味着当重复选择相同的文件时，`change`事件是不会触发的。

而实际开发过程中的需求往往是，每次选择文件后都执行一些逻辑，即使选择的文件没有变化。如何在重复选择相同的文件时也触发`change`事件呢？

其实很简单，`change`事件是在内容发生变化时触发，而`input`的`value`属性中就存储着`input`的内容，而且这个属性可读写。

要想保证类型为`file`的`input`在每次选择文件后都触发`change`事件只需要在执行完需要的逻辑后把它的`value`属性设置为某个值，比如空字符串就可以了。

{% highlight javascript linenos %}
const fileInput = document.querySelector('input[type="file"]');

fileInput.addEventListener('change', function() {
  // 执行一些逻辑

  // 将input控件的内容置空，保证change事件被触发
  this.value = '';
});
{% endhighlight %}
