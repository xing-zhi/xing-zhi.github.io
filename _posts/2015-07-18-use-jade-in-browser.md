---
layout: post
title:  "在浏览器中使用「Jade」作为模板引擎"
date:   2015-07-18 10:07:00
comments: true
categories: template jade
---
使用过`emmet`后，基本上都会喜欢上使用它高效地书写HTML，在众多模板中，Jade的语法和emmet有许多相似之处，所以当看过Jade的语法后就喜欢上了它，同时也想在浏览器中使用。

# 转换的两个阶段
将Jade文件转换为HTML文件需要经过两个阶段：编译和渲染。

## 编译
编译过程把Jade文件转换成一个函数，函数名默认为「template」，接受一个对象作为参数。

{% highlight javascript %}
function template(locals) {
  // 函数体
}
{% endhighlight %}

编译过程可以通过为命令行工具jade指定`-c`或者`--client`选项生成。
{% highlight bash %}
$ jade -c <jadefile-path> -o <output-path>
{% endhighlight %}

如果没有安装jade，使用npm全局安装即可。
{% highlight bash %}
$ npm i -g jade
{% endhighlight %}

## 渲染
渲染过程就是调用编译过程生成的函数，并且把要插值的数据传递给函数的行参`locals`。

# 在浏览器中使用
了解了Jade文件转换成HTML文件的两个过程后，我们要在浏览器端使用Jade就只需要在服务器端完成编译，在浏览器端渲染即可。

因为在浏览器端只是调用函数，所以并不会带来性能问题。

# 使用gulp自动化
要实际用于项目中，当然需要使用gulp进行自动化了。（也可以使用其他工具，比如grunt。）

## 目标
使用gulp需要实现以下目标

1. 编译所有的jade文件
2. 对每个编译的函数自定义命名，采用默认命名无法使用多个jade文件
3. 将放置在某个命名空间下，最小化全局变量
4. 将编译的文件合并为一个文件
5. 最小化

## gulpfile片段

{% highlight javascript %}
'use strict';

var gulp = require('gulp'),
    jade = require('gulp-jade'),
    tap = require('gulp-tap'),
    concat = require('gulp-concat'),
    insert = require('gulp-insert'),
    uglify = require('gulp-uglify'),
    rimraf = require('gulp-rimraf'),
    path = require('path');

var jadeFiles = 'views/jade/*.jade',
    jadeCompiled = 'views/compiled',
    jadeTemplate = 'views/templates';

gulp.task('jade-compile', function() {
  return gulp
    .src(jadeFiles)
    .pipe(tap(function(file, t) {
      var filename = path.basename(file.path, '.jade');

      return gulp
        .src(file.path)
        .pipe(jade({
          client: true,
          name: filename
        }))
        .pipe(insert.prepend('window.template.' + filename + ' = '))
        .pipe(insert.append(';'))
        .pipe(gulp.dest(jadeCompiled));
    }));
});

gulp.task('jade-template', ['jade-compile'], function() {
   return gulp
    .src(jadeCompiled + '/*.js')
    .pipe(concat('template.js'))
    .pipe(insert.prepend('window.template = window.template || {};'))
    .pipe(uglify())
    .pipe(gulp.dest(jadeTemplate));
});
{% endhighlight %}
