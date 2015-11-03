---
layout: post
title: "JavaScript中块级作用域与其他语言的不同"
date:   2015-11-03 15:50:00
comments: true
categories: javascript
---

ES6中支持了块级作用域，但是JavaScript中的块级作用域和其他语言的还是有差异的。

# 与C语言的块级作用域比较
我们先来看一个示例，比较一下C语言和JavaScript中块级作用域的不同。

{% highlight c linenos %}
#include <stdio.h>

void main() {
  int a = 1;

  if(1) {
    printf("%d\n", a);    // => 1

    int a = 2;

    printf("%d\n", a);    // => 2
  }
}
{% endhighlight %}

{% highlight javascript linenos %}
'use strict';

const a = 1;

if ( 1 ) {
  console.log(a);    // => ReferenceError: a is not defined

  const a = 2;

  console.log(a);
}
{% endhighlight %}

差异体现在第一个输出语句，C语言中使用外层作用域的变量（第7行），而JavaScript则报错（第6行）。

JavaScript中报错是因为使用`let`和`const`声明的变量同样会被hoisting，同时具有时间死区。

# 所有声明都会被提升
在[JavaScript中的「hoisting」](/javascript/2015/11/02/hoisting-in-javascript.html)中我们指出JavaScript经过两个阶段构建执行环境，在声明阶段创建了作用域内的声明的所有函数和变量，在执行阶段真正执行代码时，所有声明的函数和变量都已经存在，它们就像被提升到当前作用域的顶部。

使用`let`和`const`声明的变量也不例外。这就是为什么上面的代码片段中不输出外层作用域中的变量a的值的原因。

但是为什么结果是报错，而不是使用`var`声明时熟悉的`undefined`呢？

因为使用`let`和`const`声明的变量还具有时间死区。

# 时间死区（TDZ: temporal dead zone）
[ES6的规范中关于使用`let`和`const`声明变量](http://www.ecma-international.org/ecma-262/6.0/#sec-let-and-const-declarations)有如下内容（可以点击链接查看原内容，可读性要好一些）。

># 13.3.1 Let and Const Declarations
>NOTE **let** and **const** declarations define variables that are scoped to [the running execution context’s LexicalEnvironment](http://www.ecma-international.org/ecma-262/6.0/#sec-execution-contexts). The variables are created when their containing [Lexical Environment](http://www.ecma-international.org/ecma-262/6.0/#sec-execution-contexts) is instantiated but may not be accessed in any way until the variable’s LexicalBinding is evaluated. A variable defined by a LexicalBinding with an Initializer is assigned the value of its Initializer’s AssignmentExpression when the LexicalBinding is evaluated, not when the variable is created. If a LexicalBinding in a let declaration does not have an Initializer the variable is assigned the value undefined when the LexicalBinding is evaluated.

这部分规范的主要意思就是：当控制流进入到一个新的作用域时，（会创建一个execution context，并且作为当前execution context添加到execution context栈的顶端）该作用域中使用`let`和`const`声明的变量被添加到当前execution context的LexicalEnvironment中，变量在Lexical Environment初始化时就被创建（hoisting），但是在该变量被初始化前不能被访问（时间死区）。

关于`let`和`const`声明的变量的初始化，`const`声明的变量必须在声明时进行初始化（因为const变量不能被重新赋值），而`let`声明的变量可以不显式进行初始化，此时let变量会被初始化为`undefined`，但是和`var`声明的变量不同的是，初始化只有在代码执行到声明语句时才会被执行，在这之前访问使用`let`和`const`声明的变量会抛出错误（ReferenceError）。这就是上面的代码片段抛出错误的原因。

简单而言，时间死区就是使用`let`和`const`声明的变量时，从变量声明（声明阶段创建标识符）到执行声明语句（执行阶段执行初始化）之间的时间间隔。

# 小结
虽然ES6中引入了块级作用域，但是JavaScript中的块级作用域和其他语言中的块级作用域是有差别的，它们既具有声明提升特性，也具有时间死区。这使得在`let`和`const`声明之前访问声明的变量时会抛出`ReferenceError`错误，而不是获取作用域链中的同名变量或者返回`undefined`。
