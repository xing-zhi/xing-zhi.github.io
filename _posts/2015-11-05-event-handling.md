---
layout: post
title: "JavaScript事件"
date:   2015-11-05 13:50:00
comments: true
categories: dom
---

说明：本文只讨论IE8+。

JavaScript主要通过事件和文档进行交互。本文讨论关于事件的一些基本概念，包括事件流、添加和移除事件处理程序、事件对象、事件处理程序中的`this`和事件代理，不涉及具体的事件。

# 事件流
当在一个元素上触发一个事件时，事件不但在目标元素上触发，同时也在目标元素的祖先元素上触发。这就涉及到了事件触发的顺序。

事件在页面中不同元素上的触发顺序就是事件流，由于历史原因，存在两种完全相反的事件流：事件冒泡和事件捕获。

事件冒泡指事件先在目标元素上触发，然后向祖先元素传播，直到`document`对象或者`window`对象（包括IE9的所有现代浏览器的实现）。

事件捕获指事件先在`window`对象（包括IE9的所有现代浏览器的实现）或者`document`对象（DOM2级事件规范）触发，然后向后代元素传播，直到目标元素。

在兼容性上，所有浏览器都支持事件冒泡，包括IE9+在内的所有现代浏览器同时支持事件冒泡和事件捕获，且从`window`对象开始向下传播，并最终冒泡到`window`对象。

DOM2级事件对这两个事件流进行了整合，规定了事件流包括3个阶段：事件捕获阶段、处于目标元素阶段和事件冒泡阶段。支持该事件流的浏览器为包括IE9在内的所有现代浏览器。

# 添加和移除事件处理程序
DOM2级事件定义了`addEventListener`和`removeEventListener`用于添加和移除事件处理程序。

默认情况下，使用`addEventListener`方法添加的事件处理程序在事件冒泡阶段执行，如果需要在事件捕获阶段执行，设置第3个参数为`true`。

支持这两个方法的浏览器为IE9+，所以对于IE8需要使用IE专有的方法`attachEvent`和`detachEvent`方法。

IE专有方法具有以下差异：

+ 添加事件处理程序时，要在事件类型前添加`on`前缀，比如注册`click`事件需要使用`onclick`
+ 事件处理程序在全局作用域中运行，事件处理程序中的`this`对象为`window`（IE10+才支持严格模式，无法通过指定严格模式使`this`为`undefined`），而不是注册的元素对象
+ 添加多个事件处理程序时，按添加顺序相反的顺序执行，而`addEventListener`添加多个事件处理程序时是按添加顺序执行
+ 事件处理程序只能添加到事件冒泡阶段

兼容IE8+的添加和移除事件处理程序的方法如下：

{% highlight javascript linenos %}
const eventHandler = {
  add(el, type, handler) {
    if ( el.addEventListener ) {
      el.addEventListener(type, handler);
    } else if ( el.attachEvent ) {
      el.attachEvent(`on${type}`, handler);
    }

    throw new Error('本方法不支持该浏览器，请使用其他方法注册事件处理程序');
  },
  remove(el, type, handler) {
    if ( el.removeEventListener ) {
      el.removeEventListener(type, handler);
    } else if ( el.detachEvent ) {
      el.detachEvent(`on${type}`, handler);
    }

    throw new Error('本方法不支持该浏览器，请使用其他方法移除事件处理程序');
  }
};
{% endhighlight %}

所有浏览器还支持通过直接在事件元素上添加属性添加事件处理程序，即DOM0级事件处理程序。这个方法的优点是简单、兼容性好；缺点是只能添加到事件冒泡阶段和只能添加一个事件处理程序。

# 事件对象
当某个事件触发时会产生一个包含与该事件相关信息的事件对象，事件对象的生命周期为事件处理程序的生命周期。

除了在IE上使用DOM0级方法添加事件处理程序外，事件对象都会作为事件处理程序的第一个参数注入到事件处理程序中。

在IE上使用DOM0级方法添加事件处理程序时，事件对象不会注入到事件处理程序中，而是以全局对象`event`的形式存在。在IE上使用`attachEvent`方法添加事件处理程序时，事件对象既会注入到事件处理程序中，也会以全局对象`event`的形式存在。当需要兼容IE时，建议尽量不使用DOM0级方法添加事件处理程序。

事件对象上有很多通用的属性和方法，其中最常用的是`target`属性、`preventDefault`方法和`stopPropagation`方法。

## 目标元素
触发事件的目标元素可以通过个事件对象的`target`属性获取，它是实现事件代理的基础。

{% highlight javascript linenos %}
document.addEventListener('click', function(e) {
  const target = e.target;    // 获取触发事件的目标元素

  // 根据目标元素实现不同的逻辑
});
{% endhighlight %}

## 取消默认行为
很多事件都有默认行为，比如点击一个链接会跳转到其`href`属性指定的页面，通过调用事件对象上的`preventDefault`方法，可以取消事件的默认行为。

{% highlight javascript linenos %}
document.querySelector('a').addEventListener('click', function(e) {
  e.preventDefault();    // 取消了点击链接的默认行为

  // 实现点击链接行为
});
{% endhighlight %}

## 停止事件传播
默认情况下，事件触发后会在沿事件流传播，通过调用事件对象上的`stopPropagation`方法，可以停止事件的事件捕获或者事件冒泡。
{% highlight javascript linenos %}
document.querySelector('button').addEventListener('click', function(e) {
  e.stopPropagation();    // 停止按钮点击事件的进一步传播

  // 实现点击按钮行为
});
{% endhighlight %}

# 事件处理程序中的`this`
事件处理程序中的`this`对象通常是事件处理程序所注册的元素对象。这是实际开发中通常期望的情况。

通过DOM0级方法添加事件处理程序时，事件处理程序被当作元素对象的方法，`this`为元素对象。DOM2级方法没有改变`this`对象的值。

通过IE的`attachEvent`方法添加是事件处理程序中，`this`对象为`window`对象。

# 事件代理
利用事件冒泡可以通过在某个元素上注册一个事件处理程序来处理该元素所有后代元素上触发的事件，而目标元素可以通过事件对象的`target`属性获取。这就是事件代理，后代元素上的事件由祖先元素代理处理。

事件代理减少了注册的事件处理程序的数量和对DOM的访问次数，提升了性能。同时减少了（重复的）代码量，增加了可维护性。

# 小结
JavaScript主要通过事件和文档进行交互。

事件不仅在目标元素上触发，还在其祖先元素上触发，其触发顺序被称为事件流。事件流包括事件冒泡和事件捕获。包括IE9+的现代浏览器同时支持事件冒泡和事件捕获。IE8-只支持事件冒泡。

包括IE9+的现代浏览器，可以方便地使用`addEventListener`和`removeEventListener`方法添加和移除事件处理程序。IE8需要使用IE特有的方法`attachEvent`和`detachEvent`方法添加和移除事件处理程序。

当某个事件触发时会产生一个包含与该事件相关信息的事件对象。除了IE通过DOM0级方法添加事件处理程序时事件对象为全局`event`对象外，事件对象都会作为事件处理程序的第一个参数注入到事件处理程序中。

除了使用IE特有的`attachEvent`方法添加事件处理程序时`this`对象为`window`外，使用其他方法添加事件处理程序时，`this`对象为所注册的元素对象，

事件代理指通过在祖先元素为后代元素注册事件处理程序。事件代理减少了事件处理程序的数量和对DOM的访问次数，提升了性能。
