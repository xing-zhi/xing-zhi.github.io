---
layout: post
title:  "Currying（柯里化）和Partial Application（部分应用）"
date:   2015-08-02 12:50:00
comments: true
categories: FP
---

刚接触currying和partial application时，傻傻分不清。还一度认为它们是同一个概念的两个略有差别的版本。

随着了解的深入，才发现我错了。

# Currying

Curring是把一个接受多个参数的函数转换成一个逐步接受单个参数的函数的过程。调用转换后的函数时，当参数数量少于函数参数数时返回一个接受剩余参数的函数，当参数数量等于函数参数数时，返回函数调用结果。

**这个转换过程并没有改变函数本身，只是改变了函数的调用方式**，使得它更利于代码复用，更适合composition。

在实际实现中，转换后的函数并不限于每次只接受一个参数。

下面是一个对接受两个参数的函数进行柯里化的函数示例。

{% highlight javascript linenos %}
const curry2 = (fn) => (x) => (y) => fn.call(null, x, y);

const add = (x, y) => x + y;
const curriedAdd = curry2(add);    //=> [Function]
const add2 = curriedAdd(2);    //=> [Function]

console.log(add2(5));    //=> 7
console.log(curriedAdd(2)(5));    //=> 7
{% endhighlight %}

几个常用的工具函数库[underscore](http://underscorejs.org/)、[lodash](https://lodash.com/)和[ramda](http://ramdajs.com/)都提供了curry函数。

# Partial application

Partial application则是把一个函数转换成前面部分参数固定的函数的过程。调用转换后的函数则返回函数的结果。

这个转换过程把最初的函数**转换成了另一个函数**。通过partiial application的方式执行函数实际上是一个**两步调用过程**（application的动词apply与call、invoke都用于表示函数的调用，只是apply更多用于函数式编程中）。

通过函数的`bind`方法就能方便地实现partial application。

{% highlight javascript linenos %}
const add = (x, y) => x + y;
const partialAppliedAdd = add.bind(null, 2, 5);    //=> [Function]

console.log(partialAppliedAdd(1));    //=> 7
{% endhighlight %}

# 参考
[Currying on wikipedia](https://en.wikipedia.org/wiki/Currying)

[Partial application on wikipedia](https://en.wikipedia.org/wiki/Partial_application)
