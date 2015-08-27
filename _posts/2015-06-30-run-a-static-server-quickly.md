---
layout: post
title:  "快速运行一个静态服务器"
date:   2015-06-30 11:50:00
comments: true
categories: node
---

在前端开发中，搭建一个简单的服务器，通过HTTP协议访问我们的内容比直接在浏览器中打开HTML文件要方便、实用的多。

有时一个HTTP服务器也是必须的，比如在进行`Ajax`相关开发和测试时，本地文件协议`file://`和HTTP协议`http://`之间存在跨域问题。

下面简单介绍两种快速运行HTTP服务器的方法。

# 使用「Node.js」和「express」
现在前端开发，或多或少都会和`Node`打交道，甚至`Node`是必备技能。

`Node`结合`express`可以快速实现一个HTTP服务器。

首先安装`express`
{% highlight bash linenos %}
$ npm i -S express
{% endhighlight %}

然后是创建服务器server.js文件
{% highlight javascript linenos %}
var express = require('express'),
    app = express();

  app.use(express('public'));    // 文件夹public为HTTP服务器的根目录

  app.listen(3000, function(err) {    // 监听3000端口
    if ( err ) {
      console.error(err);
    } else {
      console.log('Server is running at http://localhost:3000');
    }
  });
{% endhighlight %}

最后，在和server.js处于相同目录下的public文件夹下添加相关文件，运行服务器
{% highlight bash linenos %}
$ node server.js
{% endhighlight %}

# 使用「Python」
如果电脑上已经安装好了`Python`，那么使用`Python`则更简单。

在某个文件夹中运行下面的命令，就可以把该文件夹变成一个HTTP服务器的根目录，同时在指定的端口上运行：
{% highlight bash linenos %}
$ python -m SimpleHTTPServer 3000
{% endhighlight %}

注：Mac上预装了`Python`；而`*nix`只要不是刚重装系统，一般也已经安装上`Python`了，即使没安装，安装也很方便；至于Windows，不是很清楚，不过使用[wamp](http://www.wampserver.com/en/)或许是一个不错的选择。
