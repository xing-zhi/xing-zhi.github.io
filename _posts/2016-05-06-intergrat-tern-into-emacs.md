---
layout: post
title:  "把tern集成进emacs，更好的进行JavaScript编程"
date:   2016-05-05 20:30:00
comments: true
categories: emacs
---

在emacs中，通过集成`tern`，可以轻松实现自动补全、提示、跳转到定义等功能。

要把`tern`集成到emacs中也很简单。

# 安装tern
{% highlight bash linenos %}
$ npm i -g tern
{% endhighlight %}

# 安装tern-mode和tern-auto-complete
在emacs中执行`M-x package-list-packages`，然后安装`tern-mode`和`tern-auto-complete`

# 配置emacs
在emacs配置文件中添加以下内容
{% highlight lisp linenos %}
(add-hook 'js-mode-hook (lambda () (tern-mode t)))
(eval-after-load 'tern
   '(progn
      (require 'tern-auto-complete)
      (tern-ac-setup)))
{% endhighlight  %}

经过这3步就完成了，happy coding！

# 参考
[tern官方文档](http://ternjs.net/doc/manual.html)
