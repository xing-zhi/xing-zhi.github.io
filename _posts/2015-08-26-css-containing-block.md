---
layout: post
title:  "CSS包含块"
date:   2015-08-26 12:50:00
comments: true
categories: css
---

CSS包含块是CSS的视觉可视化模型的一个重要的基本概念，因为每个元素都是相对于其包含块摆放的。也就是说，元素的包含块为元素的尺寸和位置的计算提供了参考，是元素的“定位上下文”。

元素的包含块与它的定位方式相关，元素定位方式不同，其包含块是不同，下面分情况说明。

# 根元素的包含块
根元素的包含块又叫初始包含块，它由用户代理（通常为浏览器）建立。HTML中的根元素是html元素。大多数浏览器中，初始包含块是一个视口大小的矩形。

# 非定位元素和相对定位元素的包含块
对于非定位元素（`position: static`）和相对定位元素（`position: relative`），它的包含块是**最近的块级祖先元素或行内块级祖先元素**的**内容边界**。

<p data-height="266" data-theme-id="18224" data-slug-hash="ZGgwvM" data-default-tab="result" data-user="xingzhi" class='codepen'>See the Pen <a href='http://codepen.io/xingzhi/pen/ZGgwvM/'>包含块示例1</a> by xingzhi (<a href='http://codepen.io/xingzhi'>@xingzhi</a>) on <a href='http://codepen.io'>CodePen</a>.</p>

# 绝对定位的元素
对于绝对定位的元素（`position: absolute`），它的包含块是**最近的定位祖先元素**（最近的position不是static的祖先元素），根据祖先元素的类型分以下3种情况：

1. 如果祖先元素是块级元素，那么包含块为该元素的**内边距边界**。

    <p data-height="266" data-theme-id="18224" data-slug-hash="EjqMax" data-default-tab="result" data-user="xingzhi" class='codepen'>See the Pen <a href='http://codepen.io/xingzhi/pen/EjqMax/'>包含块示例2</a> by xingzhi (<a href='http://codepen.io/xingzhi'>@xingzhi</a>) on <a href='http://codepen.io'>CodePen</a>.</p>

2. 如果祖先元素是行内元素，那么包含块为该祖先元素的**内容边界**，具体因祖先元素的`direction`属性的设置而有差异。
    1. 当`direction`为`ltr`时，包含块的上边界和左边界是祖先元素中**第一个框**的内容区的上边界和左边界，包含块的下边界和右边界是祖先元素中**最后一个框**的内容区的下边界和右边界。
    2. 当`direction`为`rtl`时，包含块的上边界和右边界是祖先元素中**第一个框**的内容区的上边界和右边界，包含块的下边界和左边界是祖先元素中**最后一个框**的内容区的下边界和左边界。

    <p data-height="266" data-theme-id="18224" data-slug-hash="PqMLqO" data-default-tab="result" data-user="xingzhi" class='codepen'>See the Pen <a href='http://codepen.io/xingzhi/pen/PqMLqO/'>包含块示例3</a> by xingzhi (<a href='http://codepen.io/xingzhi'>@xingzhi</a>) on <a href='http://codepen.io'>CodePen</a>.</p>

3. 如果没有定位祖先元素，那么包含块为**初始包含块**。

    <p data-height="266" data-theme-id="18224" data-slug-hash="EjqrMq" data-default-tab="result" data-user="xingzhi" class='codepen'>See the Pen <a href='http://codepen.io/xingzhi/pen/EjqrMq/'>包含块示例4</a> by xingzhi (<a href='http://codepen.io/xingzhi'>@xingzhi</a>) on <a href='http://codepen.io'>CodePen</a>.</p>

# 固定定位的元素
对于固定定位的元素（`position: fixed`）,它的包含块是**视口本身**。

<p data-height="266" data-theme-id="18224" data-slug-hash="oXKVBY" data-default-tab="result" data-user="xingzhi" class='codepen'>See the Pen <a href='http://codepen.io/xingzhi/pen/oXKVBY/'>包含块示例5</a> by xingzhi (<a href='http://codepen.io/xingzhi'>@xingzhi</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
