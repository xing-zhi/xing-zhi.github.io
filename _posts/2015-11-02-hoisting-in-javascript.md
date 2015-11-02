---
layout: post
title: "JavaScript中的「hoisting」"
date:   2015-11-02 14:20:00
comments: true
categories: javascript
---

JavaScript中变量声明存在一个被称为变量声明提升（hoisting）的现象，但是这个说法是不准确的，有误导性的，虽然它很形象地概括了这个现象。

# 示例
首先以函数声明和变量声明来两个示例。

## 函数声明提升
函数声明时函数名和函数体都会提升。
{% highlight javascript linenos %}
'use strict';

foo();    // => foo

function foo() {
  console.log('foo');
}
{% endhighlight %}

## 变量声明提升
使用`var`声明变量时，只是变量名被提升，同时被初始化为`undefined`。

{% highlight javascript linenos %}
'use strict';

console.log(a);    // => undefined

var a = 1;

console.log(a);    // => 1
{% endhighlight %}

# 分两阶段构建执行环境，并不存在提升
出现变量声明提升这个现象主要是因为JavaScript分两阶段构建执行环境：声明阶段和执行阶段。

在声明阶段，JavaScript引擎会扫描作用域内的所有函数声明和变量声明，创建相应的标识符。对于函数声明，不但会创建标识符，而且会赋值为函数体；对于变量声明，只是创建标识符，值为`undefined`。

在执行阶段，所有声明过的函数和变量都已经存在，只是变量的值是`undefined`，JavaScript引擎从上到下一行一行执行代码时可以访问该作用域内声明过的所有函数和变量。

正是在JavaScript执行之前有一个声明阶段，才产生了所谓的变量声明提升现象。

关于执行环境的构建还有很多细节，这些细节对于透彻理解JavaScript的执行很有帮助，这里不深入到这些细节，一是它们本身就需要至少一篇文章单独说明，二是ES6的规范做了很大的扩充，目前还没有针对ES6的执行环境的深入分析的文章，而ES6规范也还只是看了很少一部分，目前还写不出深入分析ES6执行环境的文章。

## 换个角度看JavaScript代码的执行
知道了JavaScript引擎分两个阶段运行JavaScript代码，我们就可以换个角度来看JavaScript代码的运行。下面就分两个阶段来“运行”上面的两个示例。

对于上面的函数声明示例：
{% highlight javascript linenos %}
// 声明阶段
function foo() {
  console.log('foo');
}
{% endhighlight %}

{% highlight javascript linenos %}
// 执行阶段
foo();    // => foo
{% endhighlight %}

对于上面的变量声明示例：
{% highlight javascript linenos %}
// 声明阶段
var a;
{% endhighlight %}

{% highlight javascript linenos %}
// 执行阶段
console.log(a);    // => undefined

a = 1;

console.log(a);    // => 1
{% endhighlight %}

## 变量声明提升这个说法具有误导性
变量声明提升这个说法是有误导性的，因为它会让人误解为JavaScript引擎会修改我们的源代码，将变量声明和函数声明提升到作用域的顶部，甚至很多人就是这么解释的。没错，这很好的解释了这个现象，但是它是错的！

# let和const声明变量
ES6增加了`let`和`const`用于声明具有块级作用域的变量，因为JavaScript仍然是分两阶段运行的，所以使用它们声明的变量也会在声明阶段创建标识符，但是因为使用它们声明的变量还具有时间死区，情况更复杂一些，会单独写一篇博客进行说明。

# 小结
JavaScript引擎分两个阶段构建执行环境，在声明阶段创建了作用域内所有的标识符；在执行阶段作用域内的声明的函数已经存在且值为函数体，声明的变量也已经存在且值为`undefined`。这就产生了所谓的变量声明提升现象。

但是变量声明提升这个说法是有误导性的，因为它会让人误解为JavaScript引擎会修改我们的源代码，将变量声明和函数声明提升到作用域的顶部。
