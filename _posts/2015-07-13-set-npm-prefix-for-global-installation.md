---
layout: post
title: 设置「npm prefix」进行全局安装
date:   2015-07-13 09:30:00
comments: true
categories: npm
---

npm模块有两种安装模式，本地安装和全局安装。两者的区别在于：本地安装用于当前项目，全局安装用于命令行工具。

全局安装在*nix系统之下需要权限

{% highlight bash linenos %}
$ sudo npm i -g <npm-module>
{% endhighlight %}

每次进行全局安装时都需要加上`sudo`并输入密码，有点麻烦。但这不是真正的问题，真正的问题是使用`sudo`**存在潜在的安全隐患**，因为npm模块可能执行任意的代码。

# 更改文件夹所有者
解决权限问题最简单粗暴的方法就是修改安装路径的所有者。
{% highlight bash linenos %}
$ sudo chown -R $USER /usr/local
{% endhighlight %}

解决了问题，但是不想修改路径的所有者该怎么办呢？

# 设置「npm prefix」
全局安装的目的是**提供命令行工具**。

在编写shell脚本时，要使自己写的脚本可以直接在命令行中执行，在写好脚本后，一般还需要做两件事：

+ 设置该脚本为可执行
+ 把该脚本的路径被添加到了`$PATH`中

所以需要作为命令行工具的npm模块只要满足这两个条件就可以直接在命令行中执行了。

## 本地安装的npm模块可以通过指定路径执行
第一个条件只要该npm模块被安装了就满足了。添加上路径，我们可以运行本地安装的npm模块
{% highlight bash linenos %}
$ ./node_modules/.bin/<npm module>
{% endhighlight %}

接下来我们要做的是在某个路径下本地安装所有需要作为命令行工具的npm模块，然后把该路径下的`node_modules/.bin`添加到`$PATH`中吗？

这样可以解决问题，但是每次安装都要切换到特定的目录，是不是很麻烦。还好有更好的解决方案。

## 设置「npm prefix」
通过设置「npm prefix」可以指定全局安装的安装路径。
{% highlight bash linenos %}
$ npm config set prefix ~/npm # ~/npm可以换成任意想要的路径
{% endhighlight %}

设置之后再进行全局安装时，安装的模块就被安装到了`~/npm`（或者设置的其他路径）中。

## 把路径添加到`$PATH`中
现在只需要把`~/npm/bin`添加到`$PATH`中就可以执行了，在`.bashrc`文件中添加如下行
{% highlight bash linenos %}
export PATH="$HOME/npm/bin":$PATH
{% endhighlight %}

# 总结
当某个npm模块需要用作命令行工具时，需要进行全局安装，这在*nix系统下会遇到权限问题。

解决权限问题最简单粗暴的方法是更改安装路径是所有者。

当不想更改路径所有者时，可以通过设置`npm prefix`并且把设置的路径下的`bin`文件夹添加到`$PATH`中。
