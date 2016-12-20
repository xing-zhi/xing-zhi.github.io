---
layout: post
title:  "使用webpack构建多页面应用"
date:   2016-12-20 19:50:00
comments: true
categories: webpack
---

webpack主要被用来进行SPA开发，不过有时候也会遇到构建多页面应用的情况。

下面就通过一个简单的demo来说一说如何使用webpack构建多页面应用。

# demo说明
为了突出重点，[demo](https://github.com/xing-zhi/blog-demos/tree/master/generate-multiple-pages-with-webpack)中采用了最简的webpack配置，同时所有页面都只是做简单的演示。

demo中一共包含3个页面：index、page1和page2，目录结构如下：

	.
	├── dist    // 产出目录
	├── node_modules
	├── package.json
	├── pages    // 入口文件
	│   ├── index.js
	│   ├── page1.js
	│   └── page2.js
	├── templates    // 模板
	│   ├── index.html
	│   ├── page1.html
	│   └── page2.html
	└── webpack.config.js

# 指定多个入口
在webpack配置中，可以通过给`entry`属性设置为一个对象来指定多个入口。

{% highlight javascript linenos %}
const path = require('path');

const pathResolve = (p) => path.resolve(__dirname, p);

module.exports = {
  entry: {
    index: pathResolve('./pages/index.js'),
    page1: pathResolve('./pages/page1.js'),
    page2: pathResolve('./pages/page2.js')
  },
  output: {
    path: pathResolve('./dist'),
    filename: '[name].js'
  }
};
{% endhighlight  %}

# 生成多个HTML
上面的配置会生成多个JS文件，但是对于多页面还需要生成多个HTML文件。我们使用`html-webpack-plugin`这个插件来根据各自的模板生成多个HTML文件。

{% highlight javascript linenos %}
const path = require('path');

const HTMLWebpackPlugin = require('html-webpack-plugin');

const pathResolve = (p) => path.resolve(__dirname, p);

module.exports = {
  entry: {
    index: pathResolve('./pages/index.js'),
    page1: pathResolve('./pages/page1.js'),
    page2: pathResolve('./pages/page2.js')
  },
  output: {
    path: pathResolve('./dist'),
    filename: '[name].js'
  },
  plugins: [
    new HTMLWebpackPlugin({
      filename: 'index.html',
      template: 'templates/index.html',
      inject: true,
      chunks: ['index']
    }),
    new HTMLWebpackPlugin({
      filename: 'page1.html',
      template: 'templates/page1.html',
      inject: true,
      chunks: ['page1']
    }),
    new HTMLWebpackPlugin({
      filename: 'page2.html',
      template: 'templates/page2.html',
      inject: true,
      chunks: ['page2']
    })
  ]
};
{% endhighlight %}

# DRY
上面的配置文件中指定入口文件和对HTMLWebpackPlugin的调用都只是简单的重复。这是需要改善的。

我们的入口文件和模板都组织在了各自的文件夹下，而且具有一一对应关系。

基于此，这样我们可以通过`glob`这个模块获取所有的入口文件的信息，然后根据这些信息生成入口文件对象；并根据入口文件对象，生成对HTMLWebpackPlugin插件的调用。

{% highlight javascript linenos %}
const path = require('path');

const glob = require('glob');
const HTMLWebpackPlugin = require('html-webpack-plugin');

const pathResolve = (p) => path.resolve(__dirname, p);

const entries = getEntries('./pages/*.js');

module.exports = {
  entry: entries,
  output: {
    path: pathResolve('./dist'),
    filename: '[name].js'
  },
  plugins: [
  // 根据入口文件对象，生成对HTMLWebpackPlugin插件的调用
    ...Object.keys(entries).map((entry) => new HTMLWebpackPlugin({
      filename: `${entry}.html`,
      template: `templates/${entry}.html`,
      inject: true,
      chunks: [entry]
    }))
  ]
};

// 根据指定的路径glob，生成入口文件对象
function getEntries(pathGlob) {
  const files = glob.sync(pathGlob);

  return files.reduce((acc, file) => {
    const extname = path.extname(file);
    const basename = path.basename(file, extname);

    acc[basename] = `./${file}`;

    return acc;
  }, {});
}
{% endhighlight %} 

使用webpack构建多页面应用就实现了，其他配置和优化根据实际项目需求进行添加就好。

# 参考
[webpack entry官方文档](https://webpack.js.org/configuration/entry-context/#entry)

[html-webpack-plugin](https://github.com/ampedandwired/html-webpack-plugin)
