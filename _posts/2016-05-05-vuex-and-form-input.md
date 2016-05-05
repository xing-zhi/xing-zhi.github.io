---
layout: post
title:  "vuex和表单"
date:   2016-05-05 20:30:00
comments: true
categories: vue
---

当使用`vue`构建大型应用时，使用`vuex`管理一部分状态是很好的选择。

但是因为`vuex`的状态要通过`mutation handler`改变，这就意味着在表单中无法使用`v-model`的双向绑定。

处理方式主要有以下2种。

# 使用`:value`而不是`v-model`
这个方法是官方文档里给出来的，具体方法如下：

{% highlight html linenos %}
<input :value="person.name" @input="updatePersonName" />
{% endhighlight %}

{% highlight javascript linenos %}
// ...
import { updatePerson } from '../vuex/person/actions';
// ...
vuex: {
  getters: {
    person({ person }) {
      return person;
    }
  },
  actions: {
    updatePerson
  }
},
methods: {
  updatePersonName(e) {
    this.updatePerson('name', e.target.value);
  }
}
// ...
{% endhighlight %}

这个方法在使用中文输入法会存在问题，所以为了处理好中文输入，需要考虑其他方法。

# 使用临时变量和`v-model`
另一个方法是使用临时变量和`v-model`，这是我更喜欢使用的方法。

{% highlight html linenos %}
<input v-model="tmpName" @input="updatePersonName" />
{% endhighlight %}

{% highlight javascript linenos %}
// ...
import { updatePerson } from '../vuex/person/actions';
// ...
data() {
  return {
    tmpName: ''
  };
},
vuex: {
  getters: {
    person({ person }) {
      return person;
    }
  },
  actions: {
    updatePerson
  }
},
methods: {
  updatePersonName() {
    this.updatePerson('name', this.tmpName);
  }
},
ready() {
  this.tmpName = this.person.name;
}
// ...
{% endhighlight %}

这个方法和上一个方法主要有3处不同：

1. 使用`v-model`进行双向绑定
2. 使用临时变量存储要改变的状态
3. 在组件生命周期的`ready`阶段给临时变量赋值

# 参考
[vuex表单处理](https://github.com/vuejs/vuex/blob/master/docs/zh-cn/forms.md)

[Vuex multiple fields / forms tips](http://forum.vuejs.org/topic/3564/vuex-multiple-fields-forms-tips/2)
