---
layout: post
title: "在JavaScript自定义事件和触发事件"
date: 2016-03-24 19:10:00
comments: true
categories: javascript
---

事件在JavaScript中处于核心位置，我们可以使用各种预先定义好的事件完成各种各样的交互。

除了使用预先定义的事件外，我们也可以使用JavaScript的不同平台本身提供的接口实现自定义事件和触发事件。

# 浏览器中自定义事件和触发事件

## `Event`构造函数

在浏览器中，我们可以使用`Event`构造函数创建一个事件某种类型事件的事件对象。`Event`构造函数接收两个参数，其中第一个参数为表示事件类型的字符串，第二个参数是可选参数，接受一个`EventInit`对象，可以用于指定事件是否冒泡（`bubbles`属性）和是否可以取消（`cancelable`属性），默认是不冒泡，也不可取消。

{% highlight javascript linenos %}
const event1 = new Event('event1');    // 不冒泡，也不能被取消

const event2 = new Event('event2', {
  bubbles: true,     // 冒泡
  cancelable: true    // 可以取消
  });
{% endhighlight %}

在创建了一个事件对象之后，就可以通过在需要触发的元素上调用`dispatchEvent`方法来在该元素上触发该事件了，如果在这个元素上注册了处理这个事件的事件处理器，那么相应的事件处理器就会执行。

{% highlight javascript linenos %}
const event = new Event('test');
const el = document.querySelector('#test');

el.addEventListener('test', (e) => {
  // 事件触发后的逻辑
});

el.dispatchEvent(event);
{% endhighlight %}

## `CustomEvent`构造函数
浏览器还提供了`CustomEvent`构造函数来创建事件对象，它和`Event`构造函数的区别在于，它的第二个参数接受的是一个`CustomEventInit`对象，它除了可以指定是否冒泡和是否可以取消外，还可以通过`detail`属性传递数据。

{% highlight javascript linenos %}
const event1 = new CustomEvent('event1');    // 不冒泡，也不能被取消

const event2 = new Event('event2', {
  detail: {
    test: 'hello world'
  },
  bubbles: true,     // 冒泡
  cancelable: true    // 可以取消
  });

el.addEventListener('test', (e) => {
  console.log(e.detail);    // => {test: 'hello world'}

  // 事件触发后的逻辑
});

el.dispatchEvent(event);
{% endhighlight %}

# Node中自定义事件和触发事件
Node提供了[events模块](https://nodejs.org/api/events.html)用于自定义事件和触发事件，我们可以通过继承`events`模块提供的`EventEmitter`类来实现具有创建和监听自定义事件的类，进而可以通过事件实现不同代码间解耦等。

{% highlight javascript linenos %}
'use strict';

const EventEmitter = require('events');

const eventInstance = new EventEmitter();

eventInstance.on('testEvent', function(data) {
  console.log(data);
});

eventInstance.emit('testEvent', 'test');    // => test
{% endhighlight %}

更多内容请参考[官方文档](https://nodejs.org/api/events.html)。

# 参考
[MDN Event api](https://developer.mozilla.org/en-US/docs/Web/API/Event)

[MDN CustomEvent api](https://developer.mozilla.org/en-US/docs/Web/API/CustomEvent)

[Creating and triggering events](https://developer.mozilla.org/en-US/docs/Web/Guide/Events/Creating_and_triggering_events)

[Document.createEvent](https://developer.mozilla.org/en-US/docs/Web/API/Document/createEvent)

[Node events模块文档](https://nodejs.org/api/events.html)

