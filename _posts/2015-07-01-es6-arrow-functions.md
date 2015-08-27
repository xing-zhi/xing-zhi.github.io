---
layout: post
title:  "「ES6」箭头函数（Arrow Functions）"
date:   2015-07-01 16:12:00
comments: true
categories: javascript es6
---

箭头函数为我们提供了一种定义函数的简洁的语法。同时，箭头函数不具有属于自己的`this`值，为解决在嵌套函数中使用外层函数`this`值的问题提供了更简洁的方案。

# 简洁的语法
箭头函数的语法可以概括为，使用箭头（=>）连接函数的参数列表和函数体。

    (para1, para2, ...) => {
      statement;
      ...
      statement;
    }

其中：

+ 箭头左边的是参数列表，当只有一个参数的时候，可以省略括号；当没有参数的时候必须使用括号占位
+ 箭头右边是函数体，当只有一条语句时，可以省略大括号，并且该语句对应的表达式的值被当作返回值在箭头函数被调用时返回。

## 示例
{% highlight javascript linenos %}
var foo = () => 'abc';    // 不接受参数，总是返回字符串'abc'

var squar = x => x * x;    // 接受一个参数，返回参数的平方

var bar = (x, y) => {    // 接受两个参数，返回其中较大的值，实际不会这么写，这里只是为了演示接受多个参数，并且函数体由多条语句构成的情况
  if ( x > y ) {
    return x;
  } else {
    return y;
  }
};
{% endhighlight %}

## 注意事项
1. 箭头函数定义的是**匿名函数表达式**，无法使用箭头函数进行函数声明
2. 函数体越简单，箭头函数语法的简洁的优势越明显；但是随着函数体变得复杂，使用箭头函数会使得可读性降低

# 没有自己的`this`和`arguments`
对于普通的函数，每个函数都有属于自己的`this`和`arguments`。

但是箭头函数没有属于自己的`this`和`arguments`，在箭头函数中访问这两个变量时，访问的都是外层作用域中的相应值。

所以，如果一个函数需要有属于自己的`this`或/和`arguments`，那么需要使用普通函数。

而正是箭头函数没有属于自己的`this`，一直困扰我们的在嵌套的内部函数中使用外层函数的`this`值的问题就可以通过箭头函数轻松解决。

## 解决嵌套的内层函数访问外层函数「this」值的问题
既然箭头函数没有自己的`this`值，只是使用外层函数的`this`值，那么一直困扰我们的在嵌套的内部函数中使用外层函数的`this`值的问题，只需要使用箭头函数就轻松解决了。

目前，我们解决这个问题，一般采用以下两种方法：

+ 创建一个临时变量（通常为that、self或者_this）保存外层函数的上下文，然后在内部函数中引用该变量
+ 使用`bind`方法，给给内层函数绑定想要的上下文

所有，不使用箭头函数，我们是这样写代码的

{% highlight javascript linenos %}
function outer() {
  var that = this;

  function inner() {
    // 使用that访问outer函数的上下文
  }
}
{% endhighlight %}

或者是这样写的
{% highlight javascript linenos %}
function outer() {
  var inner = function() {
    // this为outer函数的上下文
  }.bind(this);
}
{% endhighlight %}

而使用箭头函数，我们可以这样写
{% highlight javascript linenos %}
function outer() {
  var inner = () => {
    // this为outer函数的上下文
  };
}
{% endhighlight %}

# 总结
箭头函数为我们提供了一种定义函数的简洁的语法，但是对于函数体复杂的函数，如果使用箭头函数的形式来定义，将导致可读性很差，所以尽量只把箭头函数用于简单的函数。

箭头函数没有自己的`this`和`arguments`，而是通过词法作用域从外层作用域中获取。所以箭头函数能让我们轻松解决在嵌套的内层函数中使用外层函数的`this`值的问题。但是如果一个函数需要属于自己的`this`或/和`arguments`，请使用普通的函数定义语法。
