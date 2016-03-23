---
layout: post
title:  "使用FormData接口实现AJAX上传文件"
date:   2016-03-22 11:50:00
comments: true
categories: front-end
---

使用AJAX和JSON是现在和服务器进行通信的标准形式，但是直接这样对`file`类型的`input`的内容进行上传是无法成功的。因为对文件进行序列化后得到的是一个空对象。

{% highlight html linenos %}
<input type="file" id="file-uploader" />
{% endhighlight %}

{% highlight javascript linenos %}
const fileUploader = document.querySelector('#file-uploader');
const fileHandleUrl = '/example/file-upload';

fileUploader.addEventListener('change', () => {
  const file = this.files[0];

  console.log(JSON.stringify(file));    // => "{}"
});
{% endhighlight %}

这显然不是我们想要的。要如何才能上传成功呢？很简单，使用`FormData`接口就好了。

{% highlight javascript linenos %}
const fileUploader = document.querySelector('#file-uploader');
const fileHandleUrl = '/example/file-upload';

fileUploader.addEventListener('change', function() {
  const file = this.files[0];
  const form = new FormData();

  form.append('Content-Type', 'multipart/form-data');
  form.append('file', file);

  const xhr = new XMLHttpRequest();

  xhr.onload = () => {
    // 文件上传成功逻辑
  };

  xhr.onerror = () => {
    // 文件上传出错的逻辑
  };

  xhr.open('POST', fileHandleUrl);
  xhr.send(form);

  this.value = '';
});
{% endhighlight %}

示例中直接使用的`XMLHttpRequest`实现AJAX，实际生产中，使用[superagent](https://github.com/visionmedia/superagent)或者其他模块更好。

服务器端处理也很简单，这里以`express`为例。

{% highlight javascript linenos %}
'use strict';

const path = require('path');

const express = require('express');
const formidable = require('formidable');

const app = express();

app.use(express.static(path.join(__dirname, 'public')));

app.post('/example/file-upload', (req, res, next) => {
  const form = new formidable.IncomingForm();

  form.parse(req, function(err, fields, files) {
    // 文件在files对象中
    // 处理上传的文件的逻辑
    res.send('success');
  });
});

app.listen(3000, () => {
  console.log('http://localhost:3000');
});
{% endhighlight %}

因为使用类型为`multipart/form-data`的表单上传，所有需要使用第三方模块处理类型为`multipart/form-data`的数据，这里使用的是[formidable](https://github.com/felixge/node-formidable)，还有其他选择，使用自己认为最适合的就好。

# 参考
[Using FormData Objects](https://developer.mozilla.org/en-US/docs/Web/API/FormData/Using_FormData_Objects)

[Express 4 FormData multipart parse POST request](http://stackoverflow.com/questions/31225169/express-4-formdata-multipart-parse-post-request)


