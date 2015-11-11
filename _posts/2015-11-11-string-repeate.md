---
layout: post
title:  "创建由字符串重复多次形成的字符串"
date:   2015-11-11 21:20:00
comments: true
categories: javascript
---

在ES6中为字符串新增加了`repeate`方法，可以方便地创建由某个字符串重复多次形成的字符串。

该方法接受一个指定重复次数的自然数作为参数；当使用负数或者无穷大作为参数时会抛出`RangeError`；当使用正实数作为参数时会使用其整数部分。

{% highlight javascript linenos %}
console.log('ab'.repeate(2));    // => 'abab'
console.log('ab'.repeate(-1));    // => RangeError
console.log('ab'.repeate(Infinity));    // => RangeError
console.log('ab'.repeate(2.1));    // => 'abab'
{% endhighlight %}

很方便，在不支持ES6的浏览器中如何实现呢？

我们首先会想到使用循环，但是更好的方法是使用`Array`构造函数的一个特性和数组的`join`方法。

要使用的`Array`构造函数的特性是当`Array`的构造函数接受一个自然数n为参数时，会创建一个每一项都是`undefined`、长度为n的数组。

接下来，我们只需要使用重复的字符串作为分隔符在生成的数组上调用`join`方法就可以得到我们想要的字符串了。

{% highlight javascript linenos %}
function strRepeate(str, n) {
  if ( typeof n !== 'number' ) {
    throw new TypeError('repeate count must be a number');
  }
  if ( n < 0 ) {
    throw new RangeError('repeat count must be non-negative');
  }
  if ( n === Infinity ) {
    throw new RangeError('repeat count must be less than infinity');
  }

  n = Math.floor(n);

  return new Array(n + 1).join(str);
}
{% endhighlight %}

注意：不建议使用`Array`构造函数创建数组，建议始终使用数组字面值创建数组。

# 小结
ES6为字符串提供了`repeate`方法，用于创建由某个字符串重复多次形成的字符串。

在不支持ES6的浏览器，我们使用`Array`构造函数和数组的`join`方法也可以实现相同的功能。
