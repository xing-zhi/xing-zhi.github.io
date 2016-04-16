---
layout: post
title:  "linux常用操作"
date:   2016-04-16 14:12:00
comments: true
categories: linux
---

这里记录一些常用的linux操作，用于参考。linux版本为Debian8。

# 设置sudo
设置sudo是一项重要的权限管理手段。通过命令行设置如下：

<% highlight bash linenos %>
$ su
# apt-get install sudo
# sudo adduser <username> sudo
<% highlightend %>

# 参考
[How can I add a new user as sudoer using the command line?](http://askubuntu.com/questions/7477/how-can-i-add-a-new-user-as-sudoer-using-the-command-line)
