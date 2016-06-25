---
layout: post
title: "在JavaScript健壮地使用内置方法"
date: 2016-06-25 16:28:00
comments: true
categories: javascript
---

JavaScript提供了大量的内置方法，用于执行一些重要和常用的操作。

通常而言，只要我们不重写这些方法。直接使用他们就好了。

但是在某些情况下，或者当我们需要和第三方代码打交道，或者我们需要处理我们不能控制的数据时，这可能引发问题。

下面以对象的`hasOwnProperty`方法为例进行说明。

`hasOwnProperty`方法通常用于遍历对象属性时，对继承自原型对象的属性进行过滤。（有更好的方法实现对对象实例属性的遍历，这里不做讨论。）

{% highlight javascript linenos %}
const obj = {
  foo: 1,
  bar: 2
};

for ( let key in obj ) {
  if ( obj.hasOwnProperty(key) ) {
    // do something with own property
  }
}
{% endhighlight %}

因为`hasOwnproperty`实际存在于原型链顶端`Object.prototype`对象中，当某个对象的原型链中不包含`Object.prototype`时，就会报错。

{% highlight javascript linenos %}
const obj = Object.create(null);

obj.foo = 1;
obj.bar = 2;

for ( let key in obj ) {
  if ( obj.hasOwnProperty(key) ) {    // => Uncaught TypeError: obj.hasOwnProperty is not a function
    // do something with own property
  }
}
{% endhighlight %}

除了对象的原型链中没有`Object.prototype`时会引发问题外，在原型链上的任何地方对`hasOwnproperty`方法进行重新定义，也会引发问题。

因为对`hasOwnproperty`的搜索是从当前对象开始，沿着原型链一级一级进行的，如果在到达`Object.prototype`之前就遇到了`hasOwnProperty`属性就会使用。

{% highlight javascript linenos %}
const obj = {
  foo: 1,
  bar: 2,
  hasOwnProperty: ''
};

for ( let key in obj ) {
  if ( obj.hasOwnProperty(key) ) {    // => Uncaught TypeError: obj.hasOwnProperty is not a function
    // do something with own property
  }
}
{% endhighlight %}

对于这两种情况的解决方法也很简单，直接从`Object.prototype`中获取`hasOwnproperty`方法，然后使用`call/apply`进行调用。

{% highlight javascript linenos %}
const obj = {
  foo: 1,
  bar: 2
};

const hasOwn = Object.prototype.hasOwnproperty;

for ( let key in obj ) {
  if ( hasOwn.call(obj, key) ) {
    // do something with own property
  }
}
{% endhighlight %}

这个方法可以在很多流行的库的源码中看到。不过，一般的场景下，不需要这么复杂，直接使用就是了。在健壮性要求较高时最好用上，比如造轮子时。

当然这个方法也没有办法解决重写`Object.prototype.hasOwnProperty`的问题，不过谁要真这么玩也没办法拦着，只能默默送上一句「祝你好运」。
