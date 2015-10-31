---
layout: post
title:  "使用「classlist」简洁高效地修改元素的class属性"
date:   2015-10-31 10:50:00
comments: true
categories: html5
---

修改元素的`class`属性的通用方法是使用元素的`className`属性或者`getAttribute`和`setAttribute`方法，但是使用它们执行稍微复杂一些的操作都会很繁琐，HTML5提供了`classList`这个新的API，使得修改元素的`class`属性简洁高效了很多。

# classList简介
元素的`classList`属性是一个包含该元素的`class`属性的**只读**的**实时**[DOMTokenList](https://developer.mozilla.org/en-US/docs/Web/API/DOMTokenList)实例。

元素的`classList`属性本身是只读的，要修改元素`class`属性需要使用继承自`DOMTokenList`原型的方法和属性，通过`Object.getPrototypeOf()`方法可以得到元素的`classList`属性的原型对象就是`DOMTokenList`的prototype对象。具体见下图。

![DOMTokenList](/images/domtokenlist.png)

从上图可以看出，在元素的`classList`属性上可以通过`length`属性获取类名的个数，还可以使用多个方法对元素的`class`属性进行修改。

在DOMTokenList的众多方法中，`add`、`remove`、`toggle`和`contains`方法使得修改`class`属性变得简洁高效。

从这几个方法的名字就能看出它们的功能，这里就不介绍了，主要说一说使用过程中的几点注意：

+ 因为它们都是`DOMTokenList`方法，所以参数都必须是有效的`token`，不能包含空格，所以无法使用HTML中`class`属性使用空格分隔多个类名的形式指定多个类名。
+ `add`和`remove`方法可以同时接受多个参数，同时处理多个类名。
+ `contains`方法只接受一个参数，多余参数会被忽略。
+ `toggle`接收一个参数和一个可选的布尔值参数（`force`），当指定`force`为`true`时，作用和`add`相同；当指定`force`为`false`时，作用和`remove`相同。
+ 所有方法都无法链式调用。

# 兼容性
`classList`属性的兼容性如下图。

![classList兼容性](/images/caniuse-classlist.png)

从上图可以看出，IE又拖后腿了。在实际开发中，可以根据实际情况封装`classList`的不同方法。

下面简单实现了`classList`的`add`、`remove`、`toggle`和`contains`方法，使用了ES6特性，同时函数API和标准略有差异，**只是用于演示**。

{% highlight javascript linenos %}
function classlist(el) {
  let classString = el.getAttribute('class') || '';

  return {
    add(...classNames) {
      classNames.forEach(function(className) {
        if ( !this.contains(className) ) {
          classString += ` ${className}`;
        }
      }.bind(this));

      el.setAttribute('class', classString);

      return this;
    },
    remove(...classNames) {
      classNames.forEach(function(className) {
        classString = classString.replace(new RegExp(className), '');
      });

      el.setAttribute('class', classString);

      return this;
    },
    toggle(...classNames) {
      classNames.forEach(function(className) {
        this.contains(className) ? this.remove(className) : this.add(className);
      }.bind(this));

      return this;
    },
    contains(...classNames) {
      return classNames.every((className) => classString.indexOf(className) !== -1);
    }
  };
}
{% endhighlight %}

# 参考
[MDN Element.classList](https://developer.mozilla.org/en-US/docs/Web/API/Element/classList)

[MDN DOMTokenList](https://developer.mozilla.org/en-US/docs/Web/API/DOMTokenList)
