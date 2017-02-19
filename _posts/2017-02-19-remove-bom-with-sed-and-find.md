---
layout: post
title:  "使用sed和find删除BOM"
date:   2017-02-19 18:10:00
comments: true
categories: linux
---

页面中出现了比样式中指定的更大的空白区域，审查元素发现HTML中多出了一个`&#65279`字符，当手动把这个字符删除后，样式就正常了。

然后Google `&#65279`，发现`&#65279`是一个Unicode字符。

    The character in question &#65279 is the Unicode Character 'ZERO WIDTH NO-BREAK SPACE' (U+FEFF).

进一步Google `U+FEFF`发现`U+FEFF`实际上是`UTF8`编码文件中的`BOM`。

    That isn't <U+FEFF>, that is <0xEF,0xBB,0xBF> that is the BOM of UTF8 files

然后什么是`BOM`呢

    字节顺序标记（英语：byte-order mark，BOM）是位于码点U+FEFF的统一码字符的名称。当以UTF-16或UTF-32来将UCS/统一码字符所组成的字符串编码时，这个字符被用来标示其字节序。它常被用来当做标示文件是以UTF-8、UTF-16或UTF-32编码的记号。

可是为什么之前一直没有遇到过这个问题呢，因为类Unix系统是不会在文件中添加BOM的，而这一次服务端采用的是`C#`，环境是Windows，文件中多了这个字符。

    在类Unix系统（大量使用文本文件，用于文件格式，用于进程间通信）中，这种作法则不被建议采用。因为它会妨碍到如解译器脚本开头的Shebang等的一些重要的码的正确处理。它亦会影响到无法识别它的编程语言。如gcc会报告源码档开头有无法识别的字符。

知道了`&#65279`字符是什么，接下来就是删除它了。

很多人提到通过设置notepad++的编码方式为`以UTF-8无BOM格式编码`，然后使用notepad++打开文件并保存的方法。

对于一两个文件，这是一个不错的方法。而且任何编辑器通过设置编码，然后打开文件并保存都可以做到。但是对于大量文件还是放弃手动操作吧。

我们有代码和工具呀。

现在我们重新描述一下问题：删除当前目录下所有文件中的一个字符，这个字符十六进制表示为`<0xEF,0xBB,0xBF>`。

然后就可以使用`sed`和`find`解决这个问题了。

{% highlight bash %}
$ sed -i 's/^\xEF\xBB\xBF//' `find -type f`
{% endhighlight %}

下面进行简单的说明。

`find`不带任何参数时会搜索出当前目录及其子目录，不做任何筛选，即罗列出所有目录和文件。而`sed`只处理文件，所以添加了参数`-type f`筛选出其中的文件。

`sed`部分实现的是删除`<0xEF,0xBB,0xBF>`，通过指定参数`-i`覆盖源文件。


# 参考
[why-is-65279-appearing-in-my-html](http://stackoverflow.com/questions/9691771/why-is-65279-appearing-in-my-html)

[<U+FEFF> character showing up in files. How to remove them?](http://stackoverflow.com/questions/7297888/ufeff-character-showing-up-in-files-how-to-remove-them)

[字节顺序标记](https://zh.wikipedia.org/wiki/%E4%BD%8D%E5%85%83%E7%B5%84%E9%A0%86%E5%BA%8F%E8%A8%98%E8%99%9F)
