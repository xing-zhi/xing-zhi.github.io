---
layout: post
title:  "「ES6」模板字符串"
date:   2015-07-03 11:50:00
comments: true
categories: javascript es6
---
`ES6`中提供了一种新的定义字符串字面值的方法：模板字符串。

普通字符串由单引号（'）或者双引号（"）包裹，而模板字符串由反引号（`）包裹。

和普通字符串相比，模板字符串支持字符串内插、多行字符串。

# 字符串内插（string interpolation）
字符串内插又叫变量内插（variable interpolation），指的是将一个字符串中的占位符替换为相应值并产生最终结果的过程。

如果有C语言的经验，`printf`函数就是字符串内插很好的例子。

{% highlight c linenos %}
int foo = 3;

// %d为占位符，printf函数执行时，把%d用foo的实际值（3）替换，产生最终结果
pirntf('The value of foo is %d.', foo);
// =>The value of foo is 3.
{% endhighlight %}

在`ES6`的模板字符串中，使用美元符号和一对大括号包裹表达式（${expression}）来声明占位符。

{% highlight javascript linenos %}
var foo = 3;

console.log(`The value of foo is ${foo}.`);
// =>The value of foo is 3.
{% endhighlight %}

使用普通字符串，我们需要使用字符串连接符（+），或者使用数组的join方法来实现.
{% highlight javascript linenos %}
var foo = 3;

// 使用字符串连接符
console.log('The value of foo is ' + foo + '.');
// =>The value of foo is 3.

// 使用数组的join方法
console.log(['The value of foo is ', foo, '.'].join(''));
// =>The value of foo is 3.
{% endhighlight %}

相比之下，模板字符串更加简洁易读。

# 多行字符串
JavaScript中普通字符串是不允许换行的，跨行会引发解析错误。而要在一个字符串中使用换行需要使用转义字符（\n）。

{% highlight javascript linenos %}
// 普通字符串
var foo = 'line one\nline two';

// 可读性较差，可以使用字符串连接符或者数组的join方法增加可读性
// var foo = 'line one\n' + 'line two';
// var foo = ['line one', 'line two'].join('\n');

// 模板字符串
var bar = `line one
line two`;

console.log(foo === bar);    // =>true
{% endhighlight %}

# 带标签的模板字符串
当模板字符串紧跟一个标签（tag）时，模板字符串被这个标签表示的函数处理，结果为该函数的返回值。

标签函数的第一个参数是由模板字符串中所有字符串字面值组成的数组，从第二个参数开始依次是模板字符串中占位符的值。


{% highlight javascript linenos %}
function tag(strings) {
var values = Array.prototype.slice.call(arguments, 1),
    result = '';

  result = strings.reduce(function(result, string, index) {
    return `${result}
string[${index}]:${string}`;
  }, '')

  result = values.reduce(function(result, value, index) {
    return `${result}
value[${index}]:${value}`;
  }, result)

  return result;
}

var foo = '1',
    bar = '2';

console.log(tag`The value of foo is ${foo}, the value of bar is ${bar}.`)
// => "\nstring[0]:The value of foo is \nstring[1]:, the value of bar is \nstring[2]:.\nvalue[0]:1\nvalue[1]:2"
/* =>
string[0]:The value of foo is
string[1]:, the value of bar is
string[2]:.
value[0]:1,
value[1]:2
*/
{% endhighlight %}

由上面的输出可以看出：标签函数的第一个参数字符串数组是由模板字符串被占位符分割而成的。

其实当不对模板字符串使用标签时，使用的是默认的标签：按顺序把字符串字面值和占位符的值连接起来。
{% highlight javascript linenos %}
function defaultTag(strings, ...values) {    // 这里使用了ES6的剩余参数特性
  return strings.reduce(function(result, string, index) {
    return `${result}${string}${values[index] ? values[index] : ''}`;
  }, '');
}

var foo = '1',
    bar = '2';

console.log(tag`The value of foo is ${foo}, the value of bar is ${bar}.`)
// => The value of foo is 1, the value of bar is 2.
{% endhighlight %}

# 可维护性和安全性
模板字符串的占位符的内容可以是任何表达式，同时可以访问任何变量和函数。这带来了强大的功能，同时可能引发问题。

在模板字符串中，改变程序状态可能引发难以发现的bug。所以，为了可维护性，最好不要在模板字符串中对变量中进行改变。

同时，为了安全性，确保使用的外来模板字符串是可信赖的。

# 总结
模板字符串为我们提供了字符串内插和多行字符串的功能，同时带标签的模板字符串更是让我们能够定制处理模板字符串。但是在使用模板字符串过程了，也需要注意不在模板字符串中改变程序的状态，不使用不能确定安全性的外部模板字符串。
