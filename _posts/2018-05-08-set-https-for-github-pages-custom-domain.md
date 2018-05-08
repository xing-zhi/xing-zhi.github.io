---
layout: post
title:  "为GitHub pages个性化域名添加HTTPS支持和三级域名"
date:   2018-05-08 17:00:00
comments: true
tags:
    - GitHub pages
    - HTTPS
    - CloudFlare
    - CNAME
---
使用GitHub pages构建静态站点时，默认域名`*.github.io`已经强制使用HTTPS，但是如果我们使用个性化域名，将无法得到HTTPS支持。

基于安全考虑，HTTPS基本上成了标配。
Google搜索为HTTPS网站提供更高的权重，Chrome把HTTP网站标记为不安全。
为了实现某些功能，比如PWA，HTTPS是必要条件。

所以当我们为我们的GitHub pages站点添加个性化域名时，最好同时添加HTPPS支持。

目前使用较多的方案是使用[CloudFlare](https://www.cloudflare.com/)，因为之前搜索到的实现方法存在重定向过多的问题，在进一步搜索之后找到了没有问题的方法，记录在此。

# Prerequests
本文主要是设置HTTPS和三级域名，以下功能假设已经实现并不涉及。
+ 创建GitHub pages静态站点
+ 申请域名
+ 通过添加CNAME文件为GitHub pages静态站点设置个性化域名
+ 注册cloudflare账号并已经登陆cloudflare

接下来以我的域名`ranwu.me`为例进行说明。

# 添加HTTPS支持
## 设置DNS
点击cloudflare导航的DNS，添加两条CNAME记录：
+ ranwu.com -> xing-zhi.github.io
+ www.ranwu.com -> xing-zhi.github.io

![设置DNS](/images/cloudflare-dns.png)

从上图可以看到，我还添加了一个`blog.ranwu.me`CNAME记录。

## 设置SSL
点击cloudflare导航的Crypto，设置SSL为`Full`

![设置SSL](/images/cloudflare-ssl.png)

## 设置页面规则
点击cloudflare导航的Page Rules，添加两条页面规则
+ 设置ranwu.me总是使用HTTPS
  ![设置SSL](/images/cloudflare-page-rule-always-https.png)
+ 将www.ranwu.me永久重定向到ranwu.me
  ![设置SSL](/images/cloudflare-page-rule-redirect.png)

# 设置三级域名
有时我们为了特定的功能提供三级域名，比如使用`blog.ranwu.me`作为博客的域名。

在上面的设置中，我们其实已经实现了添加三级域名`www.ranwu.me`。
只是是为了把常用的`www.ranwu.me`重定向为`ranwu.me`。

所以要设置三级域名，只需要两步
+ 添加CNAME：blog.ranwu.me -> xing-zhi.github.io
+ 添加页面规则：设置blog.ranwu.me总是使用HTTPS

# 参考
+ [Secure and fast GitHub Pages with CloudFlare](https://blog.cloudflare.com/secure-and-fast-github-pages-with-cloudflare/)
