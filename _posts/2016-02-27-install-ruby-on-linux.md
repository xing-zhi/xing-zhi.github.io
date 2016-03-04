---
layout: post
title:  "在Debian 8下安装ruby"
date:   2016-02-27 17:50:00
comments: true
categories: linux
---

虽然不是rubyer，但是还是会遇到需要ruby环境的情况，比如搭建sass环境。

之前有装过ruby，但是现在在一个新的机器上装的时候，依然是搜索，比较不同人提供的略有差异的安装方法，然后选择一种进行安装。

重复性地搜索和比较很费事，而且没有必要，于是决定记录下安装方法，方便以后安装。

# 使用rbenv进行安装
安装ruby还有其他方法，最简单方便的是直接使用`apt`包管理工具，但是这样安装的版本较老。那就折腾一下吧。

除了直接编译源码安装外，主流的是使用`rbenv`和`rvm`，两种方法有争论，但是有不少人推荐`rbenv`，于是也采用了这个方法。

安装步骤如下：

{% highlight bash linenos %}
# 克隆rbenv
$ git clone https://github.com/rbenv/rbenv.git ~/.rbenv

# 添加"$HOME/.rbenv/bin"的路径到`PATH`变量中
$ echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
$ echo 'eval "$(rbenv init -)"' >> ~/.bashrc
$ exec $SHELL

# 克隆ruby-build，用于安装ruby
$ git clone git://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build

# 添加"$HOME/.rbenv/plugins/ruby-build/bin"的路径到`PATH`变量中
$ echo 'export PATH="$HOME/.rbenv/plugins/ruby-build/bin:$PATH"' >> ~/.bashrc
$ exec $SHELL

# 克隆rbenv-gem-rehash，在使用`gem`命令安装完`gem`后无需手动输入`rbenv rehash`命令
$ git clone https://github.com/sstephenson/rbenv-gem-rehash.git ~/.rbenv/plugins/rbenv-gem-rehash

# 列出所有可安装的ruby版本，如果这一步提示没有找到`rbenv`命令，重新开一个终端
$ rbenv install -l

# 选择一个版本进行安装，这里安装写这篇文章时的最新稳定版2.3.0，使用`-k`参数用于保存ruby的源文件
$ rbenv install 2.3.0 -k

# 设置版本
$ rbenv global 2.3.0

# 检查安装
$ ruby -v
{% endhighlight %}

通过上面的步骤，ruby就安装好并且可以使用了。但是这个方法有个很大的问题，那就是安装过程很慢。因此这次安装并不是使用`rbenv install 2.3.0`安装的ruby。

# rbenv安装慢的问题
在使用`rbenv install 2.3.0`安装ruby时，下载了很久还是在下载，而且没有任何进度提醒，差评。

于是`Ctrl-C`然后去搜索解决方案，一个被广泛使用的方法是使用淘宝镜像。方法如下：

{% highlight bash linenos %}
$ cd ~/.rbenv/versions

# 使用wget获取需要的ruby源文件，这里以写这篇文章时淘宝镜像上的最新版ruby为例
$ wget -q https://ruby.taobao.org/mirrors/ruby/ruby-2.2.2.tar.gz

# 使用下载的ruby源进行安装，将其中的<username>替换成你的用户名就可以了
# 链接末尾的#非常重要，一定要加上
$ env RUBY_BUILD_MIRROR_URL=file:///home/<username>/.rbenv/versions/ruby-2.2.2.tar.gz# ~/.rbenv/bin/rbenv install 2.2.2
{% endhighlight %}

因为是为了装最新版才折腾的，所以最终也没有采用这个方法安装，而是借鉴这个方法，下载ruby2.3.0，把文件拷贝到文件夹`~/.rbenv/versions/`中，然后安装。

{% highlight bash linenos %}
# 下载ruby2.3.0，并把文件拷贝到文件夹`~/.rbenv/versions/`中

# 使用下载的ruby源进行安装，将其中的<username>替换成你的用户名就可以了
$ env RUBY_BUILD_MIRROR_URL=file:///home/<username>/.rbenv/versions/ruby-2.3.0.tar.bz2# rbenv install 2.3.0

# 设置版本
$ rbenv global 2.3.0

# 检查安装
$ ruby -v
{% endhighlight %}

# 参考
[ruby-china的rbenv指南](https://ruby-china.org/wiki/rbenv-guide)

[Setup Ruby On Rails on Ubuntu 15.04 Vivid Vervet](https://gorails.com/setup/ubuntu/15.04)

[rbenv 安装太慢的解决办法](https://ruby-china.org/topics/14564)
