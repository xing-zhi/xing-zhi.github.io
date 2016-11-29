---
layout: post
title:  "实现双向数据绑定的个性化Vue输入组件"
date:   2016-11-29 19:00:00
comments: true
categories: vue
---

在Vue中使用`v-model`进行双向绑定是一个很方便的特性，而`v-model`指令不过是对绑定`value`属性和监听`input`事件操作的语法糖。

所以我们只需要在一个组件上

+ 绑定`value`属性，
+ 在该组件内某个状态变化时触发`input`事件

就可以在这个组件上实现双向数据绑定了。

下面就来一颗糖炒栗子，实现一个单选控件。

<p data-height="300" data-theme-id="18224" data-slug-hash="Obxxpg" data-default-tab="js,result" data-user="xingzhi" data-embed-version="2" data-pen-title="custom vue radio" class="codepen">See the Pen <a href="http://codepen.io/xingzhi/pen/Obxxpg/">custom vue radio</a> by xingzhi (<a href="http://codepen.io/xingzhi">@xingzhi</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

# 参考
[Form-Input-Components-using-Custom-Events](https://vuejs.org/v2/guide/components.html#Form-Input-Components-using-Custom-Events)
