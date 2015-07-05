---
layout: post
title:  "「let」和「const」"
date:   2015-07-05 12:26:00
categories: javascript es6
---

`ES6`终于带来了期待已久的块级作用域：`let`和`const`。

# 块级作用域
`let`和`const`声明的变量都具有块级作用域，即它们只存在于包含它们的最内层的块。

而之前的`var`声明的变量的作用域为函数作用域，即包含它们的最内层函数。

{% highlight javascript %}
function foo() {
  if ( true ) {
    let a = 'a';
    const b = 'b';
    var c = 'c'

    console.log(a);    // => 'a'
    console.log(b);    // => 'b'
    console.log(c);    // => 'c'
  }

  console.log(a);    // ReferenceError: a is not defined（抛出异常，终止执行）
  console.log(b);    // ReferenceError: b is not defined（实际并不会执行到这里）
  console.log(c);    // => 'c'（实际并不会执行到这里）
}
{% endhighlight %}

# const用于声明引用不可变的变量
const声明的并不是常量，而是**声明的变量的引用不可被改变**，即**（同一作用域内）一旦声明，不可以被再次赋值，也不可以被再次声明**，所有**声明的时候必须进行初始化**。

{% highlight javascript %}
const foo = 'foo';

// 即使是重新赋值为相同的值，也是不允许的
foo = 'foo';    // SyntaxError: Assignment to constant variable
{% endhighlight %}

使用const进行声明而不进行初始化，会抛出异常并且终止程序。

{% highlight javascript %}
const foo;    // 抛出异常，终止程序
{% endhighlight %}

如果需要声明真正意义上的常量，可以根据需要，使用Object.preventExtensions()、Object.freeze()或者Object.seal()对对象进行处理。

# let和const之间的选择
在只有`var`用于声明变量时，我们只需要使用`var`声明变量就可以了。现在有了`let`和`const`，虽然不会再使用`var`了，但是却需要在`let`和`const`之间做一个选择。

一个简单的原则是：在声明的变量所在的块中，只要该变量不会被再次赋值，就使用`const`；否则，使用`let`。

然后你会发现，大多数情况下，使用的是`const`。

{% highlight javascript %}
for ( let i = 0; i < 2; i++ ) {
  const double = i * 2;

  console.log(i, double);
  // =>
  // 0 0
  // 1 2
}
{% endhighlight %}

怎么回事？使用const声明的变量不是不可以被再次赋值的吗？这是因为变量double在循环体执行结束时被销毁，而在下一次循环时都被重新创建。

# 每次执行块时重新创建块级作用域的变量
就像函数作用域的变量生命周期为函数调用期间，即函数开始执行时创建，函数执行结束时被销毁。同一个函数的每次调用时创建的局部变量相互之间是独立的。

同样的，对于块级作用域的变量而言，它们的生命周期为该变量被创建到块执行结束期间。即同一个块在每次执行时创建的块级变量相互之间是独立的。

{% highlight javascript %}
const arr1 = [],
      arr2 = [];

for (let i = 0; i < 2; i++ ) {
  setTimeout(function() {
    arr1.push(i);
  }, 0);
}

for ( var j = 0; j < 2; j++ ){
  setTimeout(function() {
    arr2.push(j);
  }, 0);
}

setTimeout(function() {
  console.log(arr1);    // => [0, 1]
  console.log(arr2);    // => [2, 2]
}, 0);
{% endhighlight %}

在上面的代码片段中，setTimeout()用于模拟异步。从结果可以看出，当使用let时，每次循环中的变量i是相互独立的。

如果不使用let，则需要在每次循环中引入一个闭包
{% highlight javascript %}
const arr = [];

for ( var i = 0; i < 2; i++ ) {
  (function(tmp) {    // 在每次循环中引入一个闭包
    setTimeout(function() {
      arr.push(tmp);
    }, 0);
  })(i);
}

setTimeout(function() {
  console.log(arr);
}, 0);
{% endhighlight %}

# 时间死区（TDZ：temporal dead zone）
使用`var`声明的变量被提升到函数顶部，即使用`var`声明的变量的生命周期就是函数的生命周期。
{% highlight javascript %}
(function() {
  console.log(foo);    // => undefined

  var foo = 'foo';

  console.log(foo);    // => 'foo'
}();
{% endhighlight %}

所以，在“var时代”，一个最佳实践就是在函数顶部声明所有需要的变量。

使用`let`/`const`声明的变量则不一样，只有在该变量被声明后才可以被访问，这被称为“时间死区”（TDZ）。
{% highlight javascript %}
(function() {
  console.log(foo);    // => ReferenceError: foo is not defined（抛出异常，程序终止）
  console.log(bar);    // => ReferenceError: bar is not defined（实际不会执行，用于演示）

  const foo = 'foo';
  let bar = 'bar';

  console.log(foo);    // => 'foo'（实际不会执行，用于演示）
  console.log(bar);    // => 'bar'（实际不会执行，用于演示）
})();
{% endhighlight %}

需要特别指出的是：**时间死区是基于时间的，不是基于空间的**。
{% highlight javascript %}
(function() {
  function foo() {
    console.log(bar);
  }

  let bar = 'bar';

  foo();    // => 'bar'
})();
{% endhighlight %}

因为时间死区的存在，使用`let`/`const`声明变量的最佳实践现在变成了就近声明。

# 相同作用域内，不能重复声明同一个变量
在同一个作用域内，使用`var`重复声明一个变量，实际效果是什么也不做；但是使用`let`重复声明一个变量时会抛出异常。
{% highlight javascript %}
(function() {
  let foo = 'foo';

  let foo;    // SyntaxError: Identifier 'foo' has already been declared
})();
{% endhighlight %}

什么？你问`const`呢？`const`声明的变量连再次赋值都不被允许，重复声明当然也是不被允许的呀。

因为函数的行参相当于这个函数中的局部变量，所以在一个函数的顶级作用域中不能声明和行参同名的变量。
{% highlight javascript %}
(function(foo) {
  let foo = 'foo';    // SyntaxError: Identifier 'foo' has already been declared
})();
{% endhighlight %}
