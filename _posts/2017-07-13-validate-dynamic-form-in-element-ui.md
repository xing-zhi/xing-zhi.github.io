---
layout: post
title:  "在ElementUI中对动态表单进行校验"
date:   2017-07-12 19:50:00
comments: true
tags:
    - ElementUI
    - Vue
---

ElementUI表单的校验功能还是蛮好用的，不过今天遇到了一个特殊的场景，表单的一部分是固定的，另一部分是动态变化的--这一个部分可以动态的添加和删除某个相同的结构。

思考一会儿后决定尝试把整个表单拆分成到多个`el-form`组件中。
固定部分一个`el-form`组件，动态部分，相同的结构的每一个「实例」对应一个`el-form`组件。
同时对固定部分和动态部分分别写`rules`。

通过这种方式i实现了想要的效果。

Show me the code。
