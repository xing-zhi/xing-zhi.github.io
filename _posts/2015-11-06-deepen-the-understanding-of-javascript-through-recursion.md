---
layout: post
title:  "通过实现递归加深对JavaScript的认识"
date:   2015-11-06 10:50:00
comments: true
categories: javascript
---

递归就是一个函数直接或者间接调用自己。可以优雅地解决某些问题，是一项很有用的编程技术。

利用JavaScript的特性要实现递归是很简单的，写这篇博客的主要目的并不是为了说明如何实现递归，而是说明实现递归给我的一个小小的启发，从作用域的角度看待函数。

# 命名函数表达式
JavaScript中函数可以通过函数声明定义，也可以通过函数表达式定义，函数表达式又被称为函数字面值（function literal）。

函数表达式中函数名是可选的，不使用函数名就是匿名函数（表达式），使用了函数名就是命名函数表达式，函数表达式体现了JavaScript中函数作为一等对象的特性。

函数表达式中可选的函数名正是实现递归的关键：**命名函数表达式中函数名是该函数的作用域中的局部变量**。

复习了JavaScript的基础知识之后就让我们一起来实现递归吧。

# 利用命名函数表达式实现递归
实现递归的关键是在一个函数内部引用该函数，而**命名函数表达式中函数名是该函数的作用域中的局部变量**，这一特性提供了实现在一个函数内部引用该函数的最佳方法，即实现递归的最佳方法。

下面就使用命名函数表达式递归实现求阶乘。

{% highlight javascript linenos %}
'use strict';

let factorial = function factorial(n) {
  if ( n === 0 || n === 1 ) {
    return 1;
  } else {
    return n * factorial(n - 1);
  }
};

let anotherFactorial = factorial;

factorial = null;

console.log(anotherFactorial(3));    // => 6
{% endhighlight %}

在上面的代码片段中有两个factorail标识符：变量factorail和函数名factorail。变量factorail是全局变量，函数名factorail是函数factorial中的局部变量。

在定义了factorail之后，我们紧接着把函数赋值给一个新的变量anotherFactorial，接下来把变量factorial设置为`null`，然后调用函数anotherFactorial，一切正常。

这是因为在递归实现的内部，对函数自身进行引用的factorial（第7行）是该函数内的局部变量，并不依赖于外层作用域，也就是说我们的实现没有和外层作用域耦合，递归实现本身是模块化的。

接下来我们换一种方式来实现。

# 使用函数声明实现
定义函数的另一个常用方法就是使用函数声明，下面使用函数声明来实现递归，看一下有什么问题。

{% highlight javascript linenos %}
'use strict';

function factorial(n) {
  if ( n === 0 || n === 1 ) {
    return 1;
  } else {
    return n * factorial(n - 1);    // => TypeError: factorial is not a function
  }
}

let anotherFactorial = factorial;

factorial = null;

console.log(anotherFactorial(3));
{% endhighlight %}

函数声明的实现和使用命名函数表达式的实现之间的区别就只有一处，命名函数表达式换成了函数声明。

修改之后，函数名factorial是外层作用域（此处为全局作用域）中变量，函数内部引用的factorial也是该变量（此时不存在函数作用域内的局部变量factorial）。当我们将全局变量factorial设置为`null`后再调用函数时，结果为抛出`TypeError`错误，因为此时factorial是`null`，不是函数。

使用函数声明实现中，对函数自身的调用依赖于外层作用域的变量factorial，即与全局变量factorial耦合，一旦变量factorial发生变化，该实现就会出错。

# 从作用域的角度去思考
上面两种实现方式，表面上的区别是定义函数的方式不同，一个使用命名函数表达式，一个使用函数声明。

但实质上是作用域上的差别，使用命名函数表达式，对函数自身的引用通过函数内的局部变量实现，实现完全独立，没有耦合；而使用函数声明，对函数自身的引用通过外层作用域中的变量实现，是和外层作用域耦合的，一旦引用的（外层作用域）变量发生改变，实现就会出问题。

JavaScript中函数的一个重要特性就是创建新的作用域，有时从作用域而不仅仅是函数的角度去思考也许会给我们一些启发和收获。

# 小结
这篇博客的起点是在写完一个递归之后，突然冒出了要正确实现递归实际是正确处理好作用域的想法，进一步意识到我们从作用域的角度而不仅仅是函数的角度去思考问题也许会别有洞天。

但是表达的好像并不是很清楚明白。大概是还需要更多的实践来加深理解吧。
