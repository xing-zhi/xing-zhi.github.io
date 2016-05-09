---
layout: post
title: "获取数组的最后一个元素"
date: 2016-05-09 20:30:00
comments: true
categories: JavaScript
---

在JavaScript中，要获取数组的最后一个元素的常规方法是比较繁琐的，需要先计算数组的长度。

{% highlight javascript linenos %}
const arr = [1, 2, 3];
const lastElement = arr[arr.length - 1];
{% endhighlight %}

曾经稍微了解过一点Perl，虽然大部分语法都忘了，但是对它使用`-1`获取数组的最后一个元素依然印象深刻，这真的很便利！

{% highlight perl linenos %}
$array[-1]
{% endhighlight %}

但是JavaScript并不支持负数作为数组索引。

不过JavaScript中其实也提供了类似便利的方法（虽然还是稍微复杂了一点），只是一直没有意识到而已，sigh。

数组用于浅复制的`slice`方法可以接受负数作为参数，当使用负数作为参数时就表示从数组末尾开始计数。而当省略第二个可选参数时，表示一直复制到数组末尾。所以在数组上使用`slice(-1)`就可以获取数组的最后一个元素了。

{% highlight javascript linenos %}
const arr = [1, 2, 3];
const lastElement = arr.slice(-1);
{% endhighlight %}

这个方法同样适用于字符串。

# 参考
[MDN Array.prototype.slice()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/slice)
