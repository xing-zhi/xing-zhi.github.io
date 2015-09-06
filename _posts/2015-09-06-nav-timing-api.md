---
layout: post
title:  "使用「Navigation Timing」API评估网站性能"
date:   2015-09-06 11:43:00
comments: true
categories: performance
---

兵法有云「知己知彼，百战不怠」，在性能优化时如果能知道一个网站不同阶段的延时，就可以有针对性地进行性能优化。

Navigation Timing API就提供了获取一个网站的生命周期不同关键节点的时间数据的api，具体如下图所示。

![timing-overview](/images/timing-overview.png)

从上图可以看出，Navigation Timing API提供了非常细致的关键时间节点的数据，我们结合不同的数据就可以得到不同阶段的时延。

这个api的使用也非常简单，上图中所有数据（时间戳）都存储在全局变量`performance`的`timing`属性中，一个简单的属性查询就的得到了想要的数据。

下面我们利用这个api来计算从浏览器发出请求到收到响应的时延。

<p data-height="266" data-theme-id="18224" data-slug-hash="VvYNvm" data-default-tab="result" data-user="xingzhi" class='codepen'>See the Pen <a href='http://codepen.io/xingzhi/pen/VvYNvm/'>nav-timing api</a> by xingzhi (<a href='http://codepen.io/xingzhi'>@xingzhi</a>) on <a href='http://codepen.io'>CodePen</a>.</p>

在实践中我们会把相关数据传回服务器，然后进行进一步的分析，为性能优化提供真实的数据依据。

这个api的浏览器兼容性也很不错。

![navigation timing api 兼容性](/images/caniuse-nav-timing.png)

但是对于这个api来说，兼容性并不是什么问题，因为浏览器不支持并不会对用户产生影响，只是我们获取的数据样本会少一些而已。
