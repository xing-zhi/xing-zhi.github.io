---
layout: post
title:  "页面加载状态"
date:   2016-10-29 17:50:00
comments: true
categories: front-end
---

在写JavaScript代码时，我们都会把代码包裹在`document`的`DOMContentLoaded`事件回调中，以保证代码中使用的元素都已经存在了。

同时在前端面试中，`DOMContentLoaded`事件和`load`事件之间的区别也是经常被提及的问题。

实际上，页面加载过程中，除了`DOMContentLoaded`事件和`load`事件，还有一个属性`document.readyState`表示页面不同的加载状态。

`document.readyState`一共有3个值，分别表示页面加载过程中的3个状态

+ `loading`：表示页面还在加载和解析。对应的是`DOMContentLoaded`事件触发之前的状态
+ `interactive`：表示页面已经完成加载和解析，已经可以使用JavaScript和页面进行交互了，但是页面中的其他资源还在加载。对应是的`DOMContentLoaded`事件触发之后和`load`事件触发之前的状态。
+ `complete`：表示页面中所有资源都已经加载完毕。对应的即将触发`load`事件及`load`事件触发之后的状态。

除了这3个状态外，还有一个`readystatechange`事件，在`readyState`属性变化时触发。

可以看出，`readyState`属性和`readystatechange`事件可以比`DOMContentLoaded`事件和`load`事件提供更精细的页面加载状态管理。

# 兼容性
IE8+，但是IE的实现有bug

+ IE8只实现了`complete`属性
+ IE9和IE10可能在页面解析完成之前把状态修改为`iterative`

# 参考
[MDN document.readyState](https://developer.mozilla.org/en-US/docs/Web/API/Document/readyState)

