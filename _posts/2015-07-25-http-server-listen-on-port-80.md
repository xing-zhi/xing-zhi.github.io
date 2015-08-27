---
layout: post
title:  "通过设置「uid」和「gid」实现http服务器监听80端口"
date:   2015-07-25 10:10:00
comments: true
categories: node
---

http默认监听80端口，但是在*nix操作系统下，只有以`root`权限运行的程序才能监听`1～1024`范围内的端口。

所以在使用node/express开发web app时，经常指定其他端口，比如3000。跑起来了，很开心是吧。

url中添加上端口号，就可以愉快地进行测试了。

到了部署的时候，我要使用**80端口**...

使用`root`，不使用`root`，这是一个问题。不使用`root`，出现权限问题，无法监听80端口；使用`root`，一旦有人侵入...

还能不能愉快的开发了？！

等等，`root`可以做任何事，修改下「uid」和「gid」就可以了嘛。在命令行下，这事可没少干。

好了，问题解决了。

1. 以`root`权限运行server，监听80端口
2. 修改程序的「uid」和「gid」

{% highlight javascript linenos %}
// server.js
'use strict';

const http = require('http');

const cfg = require('./config.json');

http.createServer(function(req, res) {
  res.writeHead(200,{
    'Content-Type': 'text/plain'
  });
  res.end('hello port 80');
}).listen(80, function(err) {
  if ( err ) {
    console.error(err);
  }

  // 必须先更改gid，因为一旦更改了uid，就没有权限再修改gid了
  process.setgid(cfg.gid);    // 假定为1111
  process.setuid(cfg.uid);    // 假定为1111

  console.log('uid:', process.getuid());    // => 1111
  console.log('gid:', process.getgid());    // => 1111
});
{% endhighlight %}

现在使用`root`权限运行server

{% highlight bash linenos %}
$ sudo node --harmony server.js

# 输出
# uid: 1111
# gid: 1111
{% endhighlight %}

现在在浏览器中不需要指明端口号就可以访问了。

另外也可以使用`netstat`进行检测
{% highlight bash linenos %}
$ sudo netstat -antp | grep 'node'
{% endhighlight %}
