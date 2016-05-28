---
layout: post
title:  "在Debian8上安装Docker"
date:   2016-05-28 13:50:00
comments: true
categories: docker
---

# 更新源

{% highlight bash linenos %}
$ sudo apt-get update
{% endhighlight %}

# 安装`apt-transport-https`和`ca-certificates`

{% highlight bash linenos %}
$ sudo apt-get install apt-transport-https ca-certificates
{% endhighlight %}

它们通常都已经被安装到了系统中，再运行一次只是为了确保它们确实安装了。

# 添加`GPG`公钥

{% highlight bash linenos %}
$ sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
{% endhighlight %}

# 添加docker源

新建文件`/etc/apt/sources.list.d/docker.list`，然后在其中添加

    deb https://apt.dockerproject.org/repo debian-jessie main

# 更新源并安装Docker

{% highlight bash linenos %}
$ sudo apt-get update && sudo apt-get install docker-engine
{% endhighlight %}

# 验证是否正确安装

安装完成后`docker`后台进程就已经启动了，可以通过下面的命令验证Docker是否正确安装。

{% highlight bash linenos %}
$ sudo docker run hello-world
{% endhighlight %}

如果安装正确，会给出安装正确的提示。

经过以上几步Docker就安装好了。

# 参考
[Docker官方安装文档](https://docs.docker.com/engine/installation/linux/debian/)
