---
layout: post
title:  "「Nginx」的安装和开机自动启动设置"
date:   2015-09-21 11:13:00
comments: true
categories: css
---

本文主要记录在Debian8上编译安装最新版本的Nginx和设置其在开机时自动启动。

因为Debian自带的Nginx版本比较老，而这次折腾Nginx主要目的是实验HTTP/2，所以采用编译源代码的方式进行安装。

# 安装
安装的主要流程如下：

##更新系统
{% highlight bash %}
$ sudo apt-get update && sudo apt-get upgrade
{% endhighlight %}

## 安装依赖
依赖主要有用于rewrite模块的pcre库和用于ssl功能的openssl库
{% highlight bash %}
$ sudo apt-get libpcre3 libpcre3-dev openssl openssl-dev
{% endhighlight %}

## 下载最新版Nginx并且添加HTTP/2补丁
当前的最新版本是`1.9.4`，最新版本在[Nginx官网](http://nginx.org/)上查看。
{% highlight bash linenos %}
$ wget http://nginx.org/download/nginx-1.9.4.tar.gz
$ tar -zxvf nginx-1.9.4.tar.gz

$ cd nginx-1.9.4

$ wget http://nginx.org/patches/http2/patch.http2.txt
$ patch -p1 < patch.http2.txt
{% endhighlight %}

## 编译安装
配置项有很多，这里主要配置使用HTTP/2模块、HTTP ssl模块、openssl模块和pcre模块。更多配置项请参考[官方文档](http://nginx.org/en/docs/configure.html)。
{% highlight bash linenos %}
$ ./configure --with-http_v2_module --with-http_ssl_module --with-openssl --with-pcre
$ make
$ sudo make install
{% endhighlight %}

## nginx配置使用HTTP/2
要使用HTTP/2，只需要在设置https server时，把`listen`设置如下即可。

    listen  443 ssl http2;

# 设置开机自动启动
手动编译的Nginx是无法在系统启动时自动启动的。

从Debian7开始，Debian就采用`Systemd`作为init系统，所以下面采用`Systemd`的方式配置开机时自动启动Nginx。

## 创建Nginx的unit脚本
`Systemd`使用`systemctl`命令来管理init系统，一项服务通过建立一个unit脚本添加。下面就一个创建unit脚本`/lib/systemd/system/nginx.service`来添加Nginx服务。（对于`Systemd`目前也还不熟悉，后面再找时间来学习总结一下。）

{% highlight bash linenos %}
[Unit]
Description=A high performance web server and a reverse proxy server
After=network.target

[Service]
Type=forking
PIDFile=/run/nginx.pid
ExecStartPre=/usr/local/nginx/sbin/nginx -t -q -g 'daemon on; master_process on;'
ExecStart=/usr/local/nginx/sbin/nginx -g 'daemon on; master_process on;'
ExecReload=/usr/local/nginx/sbin/nginx -g 'daemon on; master_process on;' -s reload
ExecStop=-/sbin/start-stop-daemon --quiet --stop --retry QUIT/5 --pidfile /opt/nginx/logs/nginx.pid
TimeoutStopSec=5
KillMode=mixed

[Install]
WantedBy=multi-user.target
{% endhighlight %}

## 设置脚本可执行
{% highlight bash %}
sudo chmod +x /lib/systemd/system/nginx.service
{% endhighlight %}

## 使用`systemctl`启动Nginx
现在就可以使用`systemctl`的启动命令来启动Nginx了。
{% highlight bash %}
sudo systemctl start nginx
{% endhighlight %}

## 设置开机自动启动
虽然可以使用`systemctl`命令来启动Nginx了，但是目前还没有实现Nginx的开机自动启动。通过`sudo systemctl status nginx`查看Nginx的状态时会看到Loaded状态为`disabled`。

    Loaded: loaded (/lib/systemd/system/nginx.service; disabled)

`systemctl`使用`enable`和`disable`来开启和关闭一项服务的开机自动启动功能。上面的`disabled`状态表示关闭了Nginx的开机自动启动，我们来开启它。
{% highlight bash %}
sudo systemctl enable nginx
{% endhighlight %}

到这里Nginx的编译安装和设置开机自动启动就完成了。

# 参考
[Nginx Web Server on Debian 8](https://www.linode.com/docs/websites/nginx/nginx-web-server-debian-8)

[How To Use Systemctl to Manage Systemd Services and Units](https://www.digitalocean.com/community/tutorials/how-to-use-systemctl-to-manage-systemd-services-and-units)
