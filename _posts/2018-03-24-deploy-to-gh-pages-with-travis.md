---
layout: post
title:  "使用travis自动部署到gh-pages分支"
date:   2018-03-20 15:00:00
comments: true
tags:
    - github
    - gh-pages
    - travis
    - token
    - ssh
---

在开发开源项目中，通常会在`gh-pages`分支上放文档或者示例。如果每次都是手动更新很麻烦。使用travis进行持续集成自动部署就方便很多。

主要有两种方法实现，一种是使用github的[Personal Access Token](https://github.com/blog/1509-personal-api-tokens)，另一种是使用SSH。

假设我们现在在github上有一个项目，接下来我们就一步一步实现使用travis自动部署到gh-pages分支。

这里不涉及具体如何写`.travis.yml`，可以参考[官方文档](https://docs.travis-ci.com/user/customizing-the-build/)或者自行搜索。

# 相同操作
两种方法都涉及一些相同的操作。

1. 在travis官网上激活项目

   以github帐号登录[travis官网](https://travis-ci.org/)，选择项目并激活。

2. 安装travis客户端

    travis客户端是ruby包，使用gem进行安装
   ```bash
   $ gem install travis
   ```
    安装ruby可以参考[之前的文章](/linux/2016/02/27/install-ruby-on-linux.html)。

3. 登录travis

   使用github帐号登录travis
   ```bash
   $ travis login --org
   ```

4. 创建一个空的`.travis.yml`文件
   ```bash
   $ touch .travis.yml
   ```

# Token
1. 生成token

   在github生成Personal access token，路径是`Settings -> Developer Settings -> Personal access tokens`。

2. 使用travis客户端加密token
    ```bash
    $ travis encrypt -r <github-user-name>/<repo-name> GH_TOKEN=<token> --add
    ```
    在项目根目录运行以上命令，加密token。运行完成后，`.travis.yml`中会添加全局变量`secure`和`GH_TOKEN`

3. 在`.travis.yml`中添加部署到gh-pages分支的脚本

   ```yaml
   script:
   - cd examples
   - git init
   - git remote add origin https://<github-user-name>:${GH_TOKEN}@gihub.com/<github-user-name>/<ropo-name>.git
   - git add .
   - git commit -m 'Update examples'
   - git push -u -f origin master:gh-pages
   ```
   以上的实例把examples的内容推送到了gh-pages分支。需要注意的是**如果已经存在remote，要先删除再添加带token的remote，因为我们无法控制travis中克隆时是否会带token。**

# SSH
1. 生成SSH私钥公钥对，
   ```bash
   $ ssh-keygen -t rsa -C "<your_email@example.com>"
   ```
   注意不要`passphrase`，因为没有办法输入。

2. 在github上添加SSH公钥

    添加路径是`Settings -> SSH and GPG keys`。

3. 使用travis客户端加密SSH私钥
   ```bash
   $ travis encrypt-file ~/.ssh/id_rsa --add
   ```
   这里以使用`~/.ssh/id_rsa`为例。运行完成后，当前路径下会生成加密文件`id_rsa.enc`，同时`.travis.yml`中的`before_install`会自动添加生成私钥的命令。

    不过`1.8.8`客户端有一个bug，生成的命令的`-out`选项的值多了一个`\`，这会导致报错：

        ~/.ssh/id_rsa: No such file or directory

    删掉多余的`\`就好了。已经向官方提了issue，期待下个版本解决。

4. 在`.travis.yml`中添加部署到gh-pages分支的脚本

   ```yaml
   before_install:
   - openssl aes-256-cbc -K $encrypted_xxx_key -iv $encrypted_xxx_iv
-in bar_rsa.enc -out ~/.ssh/bar_rsa -d
    - chmod 600 ~/.ssh/bar_rsa
    - eval "$(ssh-agent -s)"
    - ssh-add ~/.ssh/bar_rsa
    - git config --global user.name <user-name>
    - git config --global user.email <email>
    - cd ~
    - git clone --depth=50  git@github.com:<gihub-user-name>/<repo-name>.git <dir-name>
    - cd <dir-name>
    - git checkout master
    script:
    - cd examples
    - git init
    - git remote add origin git@github.com:<github-user-name>/<repo-name>.git
    - git add .
    - git commit -m 'Update examples'
    - git push -u -f origin master:gh-pages
    after_success:
    - rm -rf ~/.ssh
    ```

# 参考
+ [使用 Travis CI 自动更新 GitHub Pages](http://notes.iissnan.com/2016/publishing-github-pages-with-travis-ci/)
+ [Authenticate with GitHub using token](https://stackoverflow.com/questions/18935539/authenticate-with-github-using-token)
+ [Travis CI Push 权限配置](https://oncemore2020.github.io/blog/travis-deploy/)
+ [Travis CI 持续部署静态站方案](https://isudox.com/2017/01/24/deploy-site-with-travis-ci/)
