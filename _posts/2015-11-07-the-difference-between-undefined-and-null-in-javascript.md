---
layout: post
title: "JavaScript中的「undefined」和「null」的区别"
date:   2015-11-07 09:50:00
comments: true
categories: javascript
---

JavaScirpt中的`undefined`和`null`都表示没有值的概念。

使用非严格相等符比较它们时结果为`true`，虽然它们都是各自类型中的唯一值。

{% highlight javascript linenos %}
conosle.log(undefined == null);    // => true
{% endhighlight %}

但是这两个值的区别是什么呢？

它们之间最大的区别，也是对实际编程有重大影响的区别在于程序本身不会产生`null`，除非我们人为赋值；而程序中的操作只要不产生有意义的值，它就会产生`undefined`，比如未初始化的变量、没有指定返回值的函数的返回值、不存在的属性值的值都是`undefined`。

{% highlight javascript linenos %}
let foo;

function bar() {}

const baz = {};

console.log(foo);    // => undefined
console.log(bar());    // => undefined
console.log(baz.foo);    // => undefined
{% endhighlight %}

从这个区别出发，我们在表示没有（有意义的）值时就最好只使用`null`，即最好永远不要在程序中人为赋值`undefined`，这样就能通过检查值是`null`还是`undefined`来区分这个值是我们在程序中有意赋值（`null`）还是操作没有产生有意义的值而自动赋值（`undefined`），提高程序的可维护性。

# 小结
对于编程实践而言，`undefiend`和`null`的区别只是当程序操作不产生有意义的值时，就会使用`undefined`；而对于`null`，除非人为赋值，一般不会产生`null`值（`Object.prototype.prototype`是个例外）。

基于这个区别，在程序中不人为赋值`undefined`，对于无意义的值一律只赋值为`null`，可以让我们通过检查值是`null`还是`undefined`来区分这个值是我们在程序中有意赋值（`null`）还是操作没有产生有意义的值而自动赋值（`undefined`），提高程序的可维护性。
