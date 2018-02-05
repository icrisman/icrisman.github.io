---
title: 如何使用Hexo在github上搭建blog
date: 2016-08-11 13:36:40
categories: tool
tags: hexo
---

原文：[How to setup a blog on github with Hexo](http://zirho.github.io/2016/06/04/hexo/)

你可以通过复制和粘贴下面的代码来创建一个blog。

相关链接：

- [Hexo docs](https://hexo.io/docs/)
- [Hexo themes](https://hexo.io/themes/)
- [github pages](https://pages.github.com/)


<!-- more -->


-----


## 准备工作

### 安装Git

- Windows: 下载&安装[git](https://git-scm.com/download/win).
- Mac: [Homebrew](http://brew.sh/), [MacPorts](http://www.macports.org/) or [installer](http://sourceforge.net/projects/git-osx-installer/).
- Linux (Ubuntu, Debian): `sudo apt-get install git-core`
- Linux (Fedora, Red Hat, CentOS): `sudo yum install git-core`


### 安装Node.js

推荐使用 nvm (亲测特别特别好用，如有需要可以通过nvm安装不同版本的node，随意切换)

cURL:

```
$ curl https://raw.github.com/creationix/nvm/master/install.sh | sh
```

Wget:

```
$ wget -qO- https://raw.github.com/creationix/nvm/master/install.sh | sh
```

安装好nvm之后，重启终端，执行下面的命令安装Node.js

```
$ nvm install 4
```

或者，你也可以直接下载安装[Node.js](http://nodejs.cn/)


### 安装Hexo

```shell
$ npm install -g hexo-cli
```


### 在github上新建repo

repo的名字必须是 <span style="color: #999;">{github用户名}.github.io</span> ，这同时也是你的blogname（原文没这么说，记得在别的文章看到过，你也可以试试用其它的可不可以）



## 搭建Hexo blog

### 创建blog项目

首先在你的workspace目录下执行下面的命令，会生成一个以blog名称命名的目录并安装好依赖

```shell
$ hexo init {blogname}
$ cd {blogname}
$ npm i
$ git init
```

### 安装Hexo主题

在[这里](https://hexo.io/themes/)找到你喜欢的主题，以及它的github地址

```
$ git submodule add {theme-github-url} themes/{theme-name}
```

将 _config.yml.example 拷贝至 _config.yml (这一步我没有做，因为下载好的主题目录下已经是这样，不需要修改)

```
$ cp themes/{theme-name}/_config.yml.example themes/{theme-name}/_config.yml
```

更新项目根目录下的 _config.yml，将theme的值替换成你下载的主题名称

```
theme: {theme-name}
```


### 修改blog配置

修改根目录的 _config.yml （不要将这个跟主题目录下的 _config.yml 搞混），主要修改Site配置和deploy配置

```
# Site
title: letica's blog                      #设置header里的title
subtitle: love and peace                  #副标题
description:
author: letica                            #作者名,会显示在左下角
email: letica@126.com                       #邮箱呗
language: zh-CN                               #设置中文
timezone:

...

deploy:
  type: git
  repo: https://github.com/letica/letica.github.io.git
  branch: master
  message: "Site updated: {{ now('YYYY-MM-DD HH:mm:ss') }}"

```


### 本地预览blog

```
$ npm i -S hexo-server
$ hexo server
```
执行命令后点击下面的链接即可预览，一般是 http://localhost:4000


### 部署blog

```
$ npm i -S hexo-deployer-git
$ hexo deploy
```

当当当，此时此刻，你应该可以在这个地址看到你的blog了 http://{blogname}.github.io.


### 将源文件上传至github （可选）

为了维护代码，你可以创建另外的分支并提交。

```
$ git remote add origin {your-git-repo-url}
$ git checkout -b source
$ git push origin source
```


### 发布新文章

创建新的post，编辑，清理，生成静态文件，部署。就是介么简单：）

```
$ hexo new '{post name}'
$ vi source/_posts/{postname}.md
$ hexo clean
$ hexo generate -d
$ hexo deploy
```


*Happy posting!*
