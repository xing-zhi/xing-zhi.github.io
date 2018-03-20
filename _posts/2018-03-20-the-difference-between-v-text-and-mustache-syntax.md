---
layout: post
title:  "Vue中数据绑定时v-text指令和mustache语法的区别"
date:   2018-03-20 15:00:00
comments: true
tags:
    - Vue
    - data binding
---

Vue中数据绑定可以使用`v-text`指令或者mustache语法。mustache语法在内部也是转化为了v-text语法。通常情况我们不需要考虑它们之间的区别，使用更加简洁的mustache语法。

不过在一些场景下我需要考虑它们之间的区别。我们来看一看它们之间的区别。

# 所在的DOM节点类型不同
在[vue 0.11.10 Directives文档](https://011.vuejs.org/api/directives.html)中我们可以看到对`v-text`指令的描述如下：
>v-text
>
>Updates the element’s textContent.
>
>Internally, {{ Mustache }} interpolations are also compiled as a v-text directive on a textNode.

mustache语法也是转化为`v-text`指令，都是更新所在节点的`textContent`属性

只是使用`v-text`指令时，变量的值对应的是所在的**HTML元素**的`textContent`属性；
使用mustache语法时，变量的值对应的是生成的**文本节点**的`textContent`属性。

实际开发中，这个区别主要在两种情况需要注意：
+ `v-text`只能在实际的HTML元素或者会渲染为HTML元素的组件上使用，不能在抽象组件上使用，比如`template`、`transition`或`keep-alive`。这种情况需要使用mustache语法。
+ mustache语法生成的是文本节点，当DOM操作会将它的内容修改为DOM节点时，会破坏文本节点（因为不能在文本节点中包含DOM节点），导致后续无法更新。这种情况需要使用`v-text`指令

# Vue未初始化时，浏览器对两种语法的解析和渲染不同
Vue未初始化时，浏览器会把mustache语法当作普通的字符串进行解析并渲染，而忽略v-text属性。

这个区别对于完整的Vue app没有影响。

但是当我们把模板写在HTML中时，比如在服务端渲染的基础上引入Vue，会出现在Vue初始化前采用mustache语法的内容被解析并渲染的情况，这会带来糟糕的用户体验。这种情况下最好使用`v-text`指令。

# 总结
在进行Vue开发过程中通常不会体会到`v-text`指令和mustache语法的区别。一般都是使用的更加简洁的mustache语法。

它们之间的区别是一个很细微的点，在开发[v-search-and-highlight插件](https://github.com/xing-zhi/v-search-and-highlight)时，对它们之间的区别有了更深入的认识，记录一下。
