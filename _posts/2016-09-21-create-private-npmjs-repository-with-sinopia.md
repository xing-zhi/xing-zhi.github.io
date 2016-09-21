---
layout: post
title:  "使用Sinopia创建私有npmjs仓库"
date:   2016-09-21 18:50:00
comments: true
categories: npm
---

在开发过程中，总有一些模块因为不具有通用性，不适合公开等原因，需要发布到私有npmjs仓库。

最简单的方法就是使用npmjs的私有模块服务，不过既然看到了这里，都是想折腾一下。

首先我们要明确，我们需要私有npmjs仓库的真正目的，也是唯一目的，是**发布私有模块**。

比较过当前主流的实现方法后，决定采用[Sinopia](https://github.com/rlidwka/sinopia)模块实现，因为它能很好地满足需求，而且简单。

# 安装
安装很简单，全局安装模块即可。

{% highlight bash linenos %}
$ npm i -g sinopia
{% endhighlight %}

这里没有使用`sudo`，是因为已经设置了`npm prefix`，具体可以参考[设置「npm prefix」进行全局安装](/npm/2015/07/13/set-npm-prefix-for-global-installation.html)。

# 运行和访问
安装好之后，运行`sinopia`命令，私有仓库就跑起来了，接下来，我们只需要访问`http://localhost:4873`就可以访问模块列表了。在实际使用中，为了方便使用，我们可以配置一个合理的域名和`80`端口。这里不赘述。

# 配置
为了使用私有仓库，我们首先需要对`npm`进行设置，使用私有仓库。

{% highlight bash linenos %}
$ npm set registry http://localhost:4873/
{% endhighlight %}

sinopia除了具有存储私有模块功能外，还具有缓存其他仓库模块，比如npmjs.org仓库，的功能。这样就可以使用其他仓库的模块了，当本地仓库没有某个模块时，就会从设置的其他仓库安装，同时缓存下来供下次使用。

具体使用哪个仓库，在配置文件中通过`proxy`属性指定。

当sinopia服务器运行起来时，会自动生成配置文件，可以找到它并对其进行修改。除了指定使用的其他仓库外，最重要的就是权限设置了，配置文件注释良好，根据实际情况修改即可。

# 发布私有模块
发布私有模块，这是我们的核心需求，要实现也非常简单。

首先添加用户。

{% highlight bash linenos %}
$ npm adduser --registry http://localhost:4873/
{% endhighlight %}

运行上面的命令，按提示操作即可。

在添加完用户之后，就可以使用`npm publish`把某个模块发布到sinopia代码仓库中了。
