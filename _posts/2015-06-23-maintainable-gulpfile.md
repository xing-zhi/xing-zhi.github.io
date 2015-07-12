---
layout: post
title:  "可维护的「gulpfile」"
date:   2015-06-23 17:50:00
comments: true
categories: gulp
---
# 可维护的`gulpfile`
随着项目的推进，往往会不断有新的任务需要自动化处理，`gulpfile`会不断“膨胀”，如果所有内容都添加到单个`gulpfile`文件中，可读性和可维护性都会变得很差。

对`gulpfile`进行合理的拆分和组织，就可以增强`gulpfile`的可读性、可维护性和可扩展性。

# 需要改写的`gulpfile`
以下面的`gulpfile`为例，对其进行拆分。因为只是为了示例，所有只定义了一个`gulp`任务。

{% highlight javascript %}
'use strict';

var gulp = require('gulp'),
    jshint = require('gulp-jshint'),
    jsSource = 'app/js/*.js';

gulp.task('lint', function() {
  return gulp
    .src(jsSource)
    .pipe(jshint())
    .pipe(jshint.reporter('jshint-stylish'));
  });

gulp.task('watch:js', ['lint'], function() {
  gulp.watch(jsSource, ['lint']);
});
{% endhighlight %}

以上就是用于改写的`gulpfile`，下面对其进行改写，首先看一下改写后的目录结构。

# 目录结构
拆分后的`gulpfile`主要包括3部分：

+ `gulpfile.js`文件：包含任务的主体逻辑，以`gulp-tasks`中的基本任务模块为基础，构建需要的任务
+ `config.json`文件：包含任务中涉及到的相关配置，比如`js`文件的路径
+ `gulp-tasks`文件夹：该文件夹下的每一个文件对应一个基本任务模块，以单一功能，可重用为书写原则

以下是`gulpfile`拆分后的目录结构：

    .
    ├── config.json
    ├── gulpfile.js
    └── gulp-tasks

# 第一步：使用`gulp-load-plugins`加载`gulp`插件
对每一个需要使用到的`gulp`插件都进行手动添加，不但麻烦，而且容易出错。

使用[gulp-load-plugins](https://www.npmjs.com/package/gulp-load-plugins)模块可以一次性加载所有的与gulp相关的模块，所有模块的引用都保存到一个对象中。

`gulp`插件在该对象中的属性名的命名方式为：去掉gulp前缀，并且把使用`-`分隔的各个部分采用驼峰命名法连接起来。比如：`gulp-jshint`在该对象中是属性名为`jshint`，`gulp-ruby-sass`在该对象中的属性名为`rubySass`。

##  安装插件

{% highlight bash %}
$ npm i -D gulp-load-plugins
{% endhighlight %}

## 通过`gulp-load-plugins`加载gulp相关插件
下面是使用`gulp-load-plugins`加载gulp相关插件后`gulpfile`
{% highlight javascript %}
'use strict';

var gulp = require('gulp'),
    $ = require('gulp-load-plugins')(),    // 所有的gulp插件对象
    jsSource = 'app/js/*.js';

gulp.task('lint', function() {
  return gulp
    .src(jsSource)
    .pipe($.jshint())
    .pipe($.jshint.reporter('jshint-stylish'));
  });

gulp.task('watch:js', ['lint'], function() {
  gulp.watch(jsSource, ['lint']);
  });
{% endhighlight %}

# 第二步：使用外部配置文件
使用外部配置文件，可以方便地对配置进行管理、修改和重用。所有与gulp相关的配置都添加到`config.json`文件中。

config.json文件
{% highlight javascript %}
{
  "tasksPath": "gulp-tasks/",
  "source": {
    "js": "app/js/*.js"
  }
}
{% endhighlight %}

gulpfile.js文件
{% highlight javascript %}
'use strict';

var gulp = require('gulp'),
    $ = require('gulp-load-plugins')(),    // 所有的gulp插件对象
    cfg = require('./config.json');    // 所有相关配置

gulp.task('lint', function() {
  return gulp
    .src(cfg.source.js)
    .pipe($.jshint())
    .pipe($.jshint.reporter('jshint-stylish'));
  });

gulp.task('watch:js', ['lint'], function() {
  gulp.watch(cfg.source.js, ['lint']);
});
{% endhighlight %}

# 第三步：把基本任务定义为一个模块
因为`gulpfile`本身就可以看作一个(?)node文件(?)，所以完全可以使用`CommonJS`模块系统把基本任务拆分成小的模块，不但易于阅读和维护，而且还便于重用。

拆分后的`lint`任务：
{% highlight javascript %}
// gulp-tasks/lint.js
'use strict';

// 每一个任务模块都接受3个参数
// gulp： gulp模块
// $： 所有的`gulp`插件对象，由`gulp-load-plugins`模块生成
// cfg：配置对象，即`config.json`文件的内容
module.exports = function(gulp, $, cfg) {
  return gulp
    .src(cfg.source.js)
    .pipe($.jshint())
    .pipe($.jshint.reporter('jshint-stylish'));
};
{% endhighlight %}

# 第四步：在`gulpfile`中获取任务模块并且使用
因为对`CommonJS`模块系统的支持，只需要在`gulpfile`中使用`require`就可以加载并且使用相关任务模块。

但是因为加载每一个任务模块都是类似的，所有有必要把加载任务模块的操作抽象为一个函数。

{% highlight javascript %}
'use strict';

var gulp = require('gulp'),
    $ = require('gulp-load-plugins')(),
    cfg = require('./config.json');

// 将获取任务模块的操作抽象为一个函数
function getTask(taskName) {
  return require(cfg.tasksPath + taskName).bind(null, gulp, $, cfg);
}

// 使用相关任务模块定义一个`gulp`任务
gulp.task('lint', getTask('lint'));

gulp.task('watch:js', ['lint'], function() {
  gulp.watch(jsSource, ['lint']);
});
{% endhighlight %}
