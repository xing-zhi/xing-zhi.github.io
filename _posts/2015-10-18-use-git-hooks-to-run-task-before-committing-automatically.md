---
layout: post
title:  "使用「git hooks」、「precommit-hook」和「gulp」在代码提交前自动运行任务"
date:   2015-10-18 10:50:00
comments: true
categories: git
---

对程序进行lint是开发过程中的一项基本操作。本文将以lint任务为例说明在代码提交前自动运行任务，本文也适用于其他任务，比如单元测试。

# 执行lint任务的时机
说明：此部分为个人观点，如果不感兴趣可以直接跳过，同时有不同观点也欢迎在评论中进行讨论。

lint任务通常被集成到自动化任务中，在代码每次被修改时都执行。

这种方法的优点是**反馈及时**，但是在集中精力coding的时候，一般会忽略掉lint的报错。原因大致有两个：一是不愿意分心；二是在修改程序的过程中，有的报错会被消除，甚至有的产生报错的代码都会被删除掉。

由此可见，在修改程序的过程中，实现相应的功能是最重要的，同时程序变化大，对程序进行lint并不是一个好的选择。

而提交代码时，当前阶段的任务已经完成，程序已经基本稳定，最重要的是提交的代码即将成为代码仓库中的一部分。

所以在提交代码前，执行lint任务，是一个不错的选择，而且有这个必要。

如何做呢？

手动运行指不定哪一次就忘了。还是自动运行吧，使用`git hooks`。

# git hooks是什么
`git hooks`用于指定在git任务执行的特定时间点自动执行的任务的脚本，脚本可以是shell脚本、node脚本等，只要脚本**返回有效的退出码（exit code）**，其中`0`表示成功，`>0`表示错误。

在运行`git init`命令初始化一个git项目时，在`.git/hooks`文件夹中会自动生成一些`hooks`示例文件。

    .git/hooks/
    ├── applypatch-msg.sample
    ├── commit-msg.sample
    ├── post-update.sample
    ├── pre-applypatch.sample
    ├── pre-commit.sample
    ├── prepare-commit-msg.sample
    ├── pre-push.sample
    ├── pre-rebase.sample
    └── update.sample

文件名中的`.sample`后缀表明它们是示例文件，并不会自动运行。

如果我们希望在git任务执行的特定时间点自动执行某些任务，只需要去掉与该时间点对应的文件的文件名中的`.sample`后缀，同时在该文件中添加的脚本即可。

对应代码提交前的`hooks`是`pre-commit`。

下面就来实现在代码提交前自动运行lint任务。

# 使用precommit-hook和gulp在代码提交前自动运行lint任务
在这里，我们不使用直接在`.git/hooks/pre-commit`中添加脚本的方法来添加`hooks`，而是使用[precommit-hook](https://github.com/nlf/precommit-hook)来自动管理。

+ 使用`npm init`初始化一个项目

{% highlight bash linenos %}
$ npm init
{% endhighlight %}

+ 使用`git init`构建git仓库
{% highlight bash linenos %}
$ git init
{% endhighlight %}

+ 安装`precommit-hook`

{% highlight bash linenos %}
$ npm i -D precommit-hook
{% endhighlight %}

安装过程中，`package.json`文件中会被添加上`scripts`和`precommit`项，通过对它们的设置可以把任何可以在命令行中运行的命令设置为`pre-commit hooks`。

+ 安装gulp和相应的插件
{% highlight bash linenos %}
$ npm i -D gulp gulp-jshint gulp-jscs gulp-jshint-stylish gulp-jscs-stylish
{% endhighlight %}

+ 添加gulp任务

示例的gulp任务中我们使用`jshint`对代码进行lint，使用`jscs`对代码进行代码风格检查。

{% highlight javascript linenos %}
'use strict';

const gulp = require('gulp'),
      jshint = require('gulp-jshint'),
      jscs = require('gulp-jscs'),
      jscsStylish = require('gulp-jscs-stylish');

gulp.task('lint', function() {
  return gulp.src('app.js')
    .pipe(jshint())
    .pipe(jshint.reporter('jshint-stylish'))
    .pipe(jshint.reporter('fail'))
    .pipe(jscs())
    .pipe(jscsStylish())
    .pipe(jscs.reporter('fail'));
});
{% endhighlight %}

注意其中的`.pipe(jshint.reporter('fail'))`和`.pipe(jscs.reporter('fail'))`，如果不指定，那么即使lint任务失败，退出码也是`0`，代码将被提交。

+ 指定precommit-hook将gulp任务添加为相应的hooks

还记得安装`precommit-hook`时在`package.json`文件中自动添加的`scripts`项和`precommit`项？现在就来修改这两项来指定代码提交前运行我们的`gulp lint`任务。

{% highlight javascript linenos %}
"scripts": {
  "lint": "gulp lint"
},
"precommit": [
  "lint"
]
{% endhighlight %}

执行完上面的步骤，我们每次执行`git commit`命令时，`gulp lint`命令就会自动执行，妈妈再也不用担心我忘记运行lint任务了。

# 参考
[Customizing Git - Git Hooks](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks)

[Gulp returns 0 when tasks fail](http://stackoverflow.com/questions/21688613/gulp-returns-0-when-tasks-fail)
