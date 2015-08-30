---
layout: post
title:  "「process.nextTick」和「setImmediate」的区别"
date:   2015-08-30 11:50:00
comments: true
categories: javascript async
---

`process.nextTick`和`setImmediate`的区别主要在于：`process.nextTick`在本次事件循环结束之后立即执行；而`setImmediate`在下次事件循环的`I/O事件回调`之后，`setTimeout`和`setInterval`之前执行。

而它们之间的区别是与它们的设计意图相关的，`process.nextTick`主要用于确保函数始终异步，也就是[行为一致性](./consistent-behavior-of-async-js.html)；而`setImmediate`主要用于[分割耗时任务](../29/splitting-long-running-task.html)。而且它们的实现确保了尽可能高效地执行，这也是使用`setTimeout`同样可以实现这两个功能却不推荐使用的原因。

接下来，我们需要做的事就是按它们的设计意图使用它们。
