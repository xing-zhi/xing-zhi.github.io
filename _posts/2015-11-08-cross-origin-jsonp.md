---
layout: post
title: "跨域之JSONP"
date:   2015-11-08 16:37:00
comments: true
categories: security
---

JSONP是CORS之前使用最广泛的实现跨域请求的方法之一。

JSONP其实很简单，只要明白了JSONP包括服务器端和浏览器端两部分，然后了解服务器端的简单实现，一切豁然开朗。

我们就先从实现开始，有了感性认识和实现的成就感之后要理解JSONP就很容易了。

# 服务器端实现
要使用JSONP，需要服务器端的支持，下面是一个使用express实现的支持JSONP的非常简单的服务器。

{% highlight javascript linenos %}
'use strict';

const express = require('express');

const app = express();

app.set('jsonp callback name', 'cb');    // 设置回调函数名为"cb"，默认是"callback"

app.use(express.static('public'));

app.get('/jsonp', function(req, res) {
  const data = {    // 实际会涉及读取数据等操作及相关逻辑，这里为了演示，直接提供数据
    test: 'test'
  };

  const cb = req.query.cb;    // 从query对象中获取回调函数名

  res.send(`${cb}(${JSON.stringify(data)})`);    // 返回函数调用字符串，在浏览器端会被自动解析并执行

  // express在res对象上添加了jsonp方法，使实现jsonp变得很简单，实现如下（不利于理解JSONP的原理）
  // res.jsonp(data);
});

app.listen(3000);

console.log('http://localhost:3000');
{% endhighlight %}

服务端实现主要包括3部分：

1. 读取和处理请求的数据，示例代码中为了简化，直接提供了数据
2. 从请求url的query字符串中获取浏览器端希望调用的函数名
3. 响应函数调用字符串，实参为序列化后的数据

需要特别强调指出的是：**服务器端的响应不是函数调用，而是内容是函数调用的字符串，该字符串（响应）到达浏览器后会被自动解析并执行**。

# 浏览器端实现
作为前端开发者，想必对JSONP的浏览器端实现已经很熟悉了，下面是一个非常简单的实例。

{% highlight javascript linenos %}
'use strict';

function jsonpTest(data) {
  console.log(data);
}

const script = document.createElement('script');    // script元素可以用来加载跨域的脚本

script.src = 'http://example.com/jsonp?cb=jsonpTest';    // 跨域请求的资源，在url参数中指定要执行的回调（jsonpTest），即数据的消费者

document.querySelector('head').appendChild(script);    // 将script元素添加到head元素末尾，发起请求
{% endhighlight %}

浏览器端实现也包括3部分

1. 创建一个`script`元素（`script`元素可以加载跨域的JavaScript脚本）
2. 将`script`元素的`src`属性设置为要请求的跨域资源的url，url中包含一个查询参数，参数名为和服务器约定的指定回调的键名，默认为`callback`；参数值为要在请求的数据上调用的函数名
3. 将`script`元素添加到文档`head`元素末尾，发起请求。当响应到达浏览器时，会被自动解析和执行

# 什么是JSONP
从JSONP的服务端和浏览器端的实现中我们可以看出，实现JSONP的核心是服务器响应的内容，即

{% highlight javascript linenos %}
callback(jsonData)
{% endhighlight %}

这个表达式表示一个函数调用，函数的实参是请求的json数据，而函数是json数据的消费者。

从这里我们也能看出JSONP名称的由来，JSONP是JSON with padding的简写，翻译为填充式JSON或参数式JSON，即（请求的）JSON数据作为希望执行的函数的参数（填充）。

理解了JSONP的含义后，现在结合实现，我们来总结一下什么是JSONP。

JSONP是利用`script`元素可以请求跨域脚本的特性的一种跨域方法。在需要请求跨域数据时，动态生成一个`src`属性为要请求的json数据的url，且url的查询参数包含要消费该数据的回调函数名的`script`元素，然后把生成的`script`元素添加到页面中以发起请求。服务器在接收到jsonp请求后，处理请求并生成json数据，然后以序列化的json数据作为回调的参数的形式生成一个字符串作为响应。浏览器在接收到响应后自动解析并执行这个函数调用，实现我们跨域请求数据的目的。

# JSONP的问题
使用JSONP实现跨域很简单，但是有不少问题，在功能上只能实现GET请求；在实现上要检测并处理错误比较麻烦（需要使用定时器定期检查是否接收到响应）。

当然JSONP最大的问题是它具有安全隐患，比如易受到XSS和CSRF攻击，在能使用CORS时最好使用CORS而不是JSONP。

对于前端安全问题，后面会专门进行总结，到时候再添加相关的内容。

# 小结
JSONP是CORS之前使用最广泛的实现跨域的方法之一。

JSONP是利用`script`元素可以请求跨域脚本的特性的一种跨域方法。在需要请求跨域数据时，动态生成一个`src`属性为要请求的json数据的url，且url的查询参数包含要消费该数据的回调函数名的`script`元素，然后把生成的`script`元素添加到页面中以发起请求。服务器在接收到jsonp请求后，处理请求并生成json数据，然后以序列化的json数据作为回调的参数的形式生成一个字符串作为响应。浏览器在接收到响应后自动解析并执行这个函数调用，实现我们跨域请求数据的目的。

JSONP简单易用，但是只能进行GET请求，需要通过使用定时器来检查响应的形式来检测并处理错误，更严重的是具有较大的安全隐患，在能使用CORS时最好使用CORS而不是JSONP。

