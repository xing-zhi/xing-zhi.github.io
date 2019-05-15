---
layout: post
title:  "MongoDB在Mac上因too many open files崩溃解决方案"
date:   2019-05-15 18:00:00
comments: true
tags:
    - MongoDB
    - Mac
---

因为写入的数据量大了一点，MongoDB在Mac总是崩溃，查看日志，显示原因是`too many open files`。

搜索之后，得出原因是Mac上对`file descriptors`数量限制很低。

实践后真正有效的是添加两个plist文件，具体方法参考[El Capitan ulimit shenanigans](https://blog.dekstroza.io/ulimit-shenanigans-on-osx-el-capitan/)。这里就不重复了

如果这个方法还是没有解决问题，那么看一下我踩的另一个坑，是不是会帮助到你。

我的MongoDB是通过homebrew安装的，它默认配置的数据存储路径是`/usr/local/var/mongodb`。

而这个路径在Mac系统的SIP安全技术的限制下，这使得上述方法也无法生效。

解决方法就是按照惯例把数据存储路径修改为`/data/db`，或者其他不在SIP限制的路径下。

# 参考
+ [El Capitan ulimit shenanigans](https://blog.dekstroza.io/ulimit-shenanigans-on-osx-el-capitan/)
+ [About System Integrity Protection on your Mac](https://support.apple.com/en-us/HT204899)
