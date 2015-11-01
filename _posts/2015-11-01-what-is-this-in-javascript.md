---
layout: post
title:  "JavaScript中如何确定「this」的值"
date:   2015-11-01 15:10:00
comments: true
categories: javascript
---

JavaScript中的`this`指函数上下文（function context），**在函数被调用时自动创建，由函数的调用方式决定**。也就是说**函数的调用方式**决定了该函数中`this`的值，`this`叫函数调用上下文似乎更准确一些。

# 「this」的值由函数的调用方式决定
在JavaScript中，函数有4种调用方式，`this`值也就对应了4种值：

+ 直接以函数形式调用，`this`的值为`undefined`（严格模式）或者`global`/`workerGlobal`（非严格模式）
+ 作为对象方法调用，`this`的值为该对象
+ 使用new操作符调用，`this`的值为新创建的对象
+ call()和apply()显式指定`this`的值，`this`的值为指定的值

# 两个特例
`this`值的确定除了由函数调用方式决定的通用法则外，还有两个特例：事件处理程序和定时器回调中的`this`值。

## 事件处理程序
事件处理程序中，`this`值为注册事件处理程序的元素对象，通过查看调用栈，事件处理程序是以函数的形式调用的，如下图所示。

![事件处理程序的调用栈](/images/callstack-of-eventlisener.png)

从调用方式来看，`this`值应该是`undefined`，但是这在事件处理程序中没有意义，而`this`值是注册事件程序所在的元素对象才有实际意义，也是在实际编程中通常期望的值。

## 定时器中的「this」
在定时器（`setTimeout`和`setInterval`）和`requestAnimationFrame`的回调中，无论是否使用严格模式，`this`值一律是`global`或者`workerGlobal`。

这是不合理的，这个问题也引起了[激烈的讨论](https://esdiscuss.org/topic/use-strict-vs-settimeout)。

# ES6中的「词法this」
ES6引入了箭头函数，它的一个重要特征是**该函数没有`this`对象，而是通过词法作用域链获取`this`**。

# 「this」与作用域
对于普通函数而言，函数被调用时，在该函数的作用域中就会自动创建`this`对象，每个函数中都会有一个局部变量`this`，所以无法直接通过`this`标识符获取到外层函数的`this`值。

要获取外层函数中的`this`通常通过设置一个临时变量保存外层函数的`this`然后通过词法作用域获取，或者使用`bind`方法绑定外层函数的`this`。

{% highlight javascript linenos %}
function outer() {
  const context = this;

  function inner() {
    // 可以使用context访问到outer中的this
  }

  innerBindThis = function() {
    // this为函数outer中的this
  }.bind(this);
}
{% endhighlight %} 

但是`this`和所有其他标识符一样，**遵循词法作用域规则**。这也是ES6中的箭头函数没有创建`this`对象时访问到外层函数的`this`的原因。

# 绑定「this」
在实际编程中会遇到需要某个函数以特定的上下文运行的情景，这时可以使用函数的`bind`方法创建一个绑定了`this`对象的函数。

绑定`this`常在事件处理程序中使用。
{% highlight javascript linenos %}
const obj = {
  count: 0,
  increment() {
    this.count++;
  }
};
const btn = document.querySelector('.btn');

btn.addEventListener('click', obj.increment);    // => this为btn对象，出错
btn.addEventListener('click', obj.increment.bind(obj));    // => 绑定this为obj
{% endhighlight %}

# 小结
JavaScript中，`this`对象**在函数被调用时自动创建，由函数的调用方式决定**。但是有两个特例：事件处理程序中，`this`的值是注册事件程序的元素对象；定时器和`requestAnimationFrame`的回调中，`this`的值是`global`或`workerGlobal`，无论是否使用严格模式。

`this`和任何标识符一样，**遵循词法作用域规则**，因为普通函数在被调用时会自动创建`this`对象，每个函数中都有一个局部变量`this`，所以无法直接通过外层函数中的`this`值。而ES6中的箭头函数不会创建`this`局部变量，可以直接获取到外层函数中的`this`。

当需要访问外层函数中的`this`时，可以使用箭头函数、使用临时变量保存外层函数的`this`然后通过该临时变量访问或者使用`bind`方法绑定外层函数的`this`。绑定`this`还通常用于某个函数需要使用某个特定对象作为上下文的情况。

# 参考
["use strict" VS setTimeout](https://esdiscuss.org/topic/use-strict-vs-settimeout)

[Arrow This](http://blog.getify.com/arrow-this/)
