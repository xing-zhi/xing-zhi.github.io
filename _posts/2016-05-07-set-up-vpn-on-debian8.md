---
layout: post
title: "Debian8设置VPN"
date: 2016-05-07 13:20:00
comments: true
categories: linux
---

当设置VPN时，在点击了添加VPN后，只有一个「从文件导入...」选项，而没有设置PPTP或者OpenVPN之类的选项。

这是缺少相应的Gnome NetworkManager插件造成的，安装相应的插件的好了。

{% highlight bash linenos %}
$ sudo apt-get install network-manager-openvpn network-manager-openvpn-gnome network-manager-pptp network-manager-pptp-gnome
{% endhighlight %}

然后重启Gnome NetworkManager。

{% highlight bash linenos %}
$ sudo NetworkManager restart
{% endhighlight %}

然后就可以设置了，享受自由的世界吧！

# 参考
[Add VPN button is disabled in Debian Wheezy/LXDE](http://unix.stackexchange.com/questions/135012/add-vpn-button-is-disabled-in-debian-wheezy-lxde)
