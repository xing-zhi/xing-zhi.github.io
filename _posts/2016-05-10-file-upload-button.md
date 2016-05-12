---
layout: post
title: "文件上传按钮"
date: 2016-05-10 20:30:00
comments: true
categories: front-end
---

使用类型为`file`的`input`元素（后面简称为「文件上传控件」）可以轻松地实现文件上传，但是默认的文件上传控件样式比较丑，同时选择的文件被显示出来。

实现一个简单的文件上传按钮，用户点击这个按钮就弹出选择文件的对话框，当文件选择后执行相应的逻辑是一个不错的选择。

下面展示两种实现文件上传按钮的方法。

# 把透明的文件上传控件覆盖在按钮上方

当点击文件上传控件时就弹出文件选择对话框，一种实现的思路就是把文件上传控件设置为透明，同时覆盖在按钮上方。

{% highlight html linenos %}
<button id="file-uploader">
  上传文件
  <input type="file" id/>
</button>
{% endhighlight %}

{% highlight css linenos %}
#file-uploader {
    position: relative;
}
#file-uploader input {
    opacity: 0;
    position: absolute;
    left: 0;
    right: 0;
    top: 0;
    bottom: 0;
    cursor: pointer;
    max-width: 100%;
}
{% endhighlight %}

# 隐藏文件上传控件，同时使用自定义事件触发点击事件

弹出文件选择对话框的关键是文件上传控件上触发了点击事件，那么只需要在点击了一个按钮之后在文件上传按钮上触发点击事件就可以了，这可以通过自定义事件实现。关于自定义事件可以参考[在JavaScript自定义事件和触发事件](/javascript/2016/03/25/create-custom-event-and-triger-event-in-javascript.html)。

{% highlight html linenos %}
<button id="file-upload-btn">上传文件</button>
<input type="file" id="hide-file-input" style="display: none"/>
{% endhighlight %}

{% highlight javascript linenos %}
const hideFileInput = document.querySelector('#hide--file-input');
document.querySelector('#file-upload-btn').addEventListener('click', function() {
  const event = new Event('click');

  hideFileInput.addEventListener('click', function() {
    console.log('clicked');
  });

  hideFileInput.dispatchEvent(event);
});
{% endhighlight %}

使用上面的两种方法都可以轻松地实现一个文件上传按钮，至于对选择的文件的处理，只需要在文件上传控件的`change`事件处理函数中执行就可以了。
