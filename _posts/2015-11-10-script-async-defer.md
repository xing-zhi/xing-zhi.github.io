---
layout: post
title:  "script的「async」和「defer」属性"
date:   2015-11-10 10:50:00
comments: true
categories: performance
---

对于一个包含外部样式表和外部脚本的网页而言，当页面解析到`<script>`标签时，会下载并执行脚本，同时暂停页面的解析，即**脚本的执行阻塞页面的解析**。

另外，脚本的执行需要等到CSSOM构建完成，即**脚本的执行被CSSOM的构建阻塞**。这就是样式表需要尽快加载并解析完毕的原因。

**脚本的执行阻塞页面的解析**和**脚本的执行被CSSOM的构建阻塞**对性能有很大的影响，HTML4引入了`defer`属性、HTML5引入了`async`属性来改变页面解析过程中的默认行为从而提升性能。

`defer`属性和`async`属性都不会阻塞页面的解析，同时都只能用于外部脚本。

# `defer`属性
`defer`属性的作用是延迟执行脚本，脚本不阻塞页面的解析，而是**在页面解析完毕后按指定的顺序执行**，并且在`DOMContentLoaded`事件触发之前执行。

使用`defer`属性可以确保脚本的加载和执行不阻塞页面的解析，同时可以保证脚本按指定的顺序执行，可以保证脚本之间的依赖关系。

但是现实情况是，延迟脚本不一定按顺序执行，也不一定在`DOMContentLoaded`事件之前执行。

最有代表性的是在IE9-中的一个bug，当两个添加了`defer`属性的JavaScript脚本，如果第一脚本修改了DOM，那么第二个脚本会在第一个脚本执行完之前开始执行。关于这个bug的更多讨论可以参考[script\[defer\] doesn't work in IE<=9](https://github.com/h5bp/lazyweb-requests/issues/42)。

# `async`属性
`async`属性的作用是异步加载脚本，脚本不阻塞页面的解析，**脚本在下载完毕后立即执行，不保证执行顺序**。一定在`load`事件触发之前执行。

# 兼容性
`defer`属性从IE5.5就开始支持，但是在IE9-中有bug，不能保证脚本按指定的顺序执行。在使用过程中需要注意。
![defer属性兼容性](/images/caniuse-defer-attribute.png)

`async`属性直到IE10才支持。
![async属性兼容性](/images/caniuse-async-attribute.png)

在实际使用中，还是需要把脚本放在页面的底部。

# preloader scanner
浏览器制造商很早就意识到了脚本阻塞页面解析对性能的影响，包括IE8在内的所有现代浏览器都实现了preloader scanner。

preloader scanner是一个轻量解析器，在页面解析被脚本阻塞时对页面的剩余部分进行解析，请求外部资源，确保页面中的资源尽快被请求和加载。

# 参考
[How the Browser Pre-loader Makes Pages Load Faster](http://andydavies.me/blog/2013/10/22/how-the-browser-pre-loader-makes-pages-load-faster/)
