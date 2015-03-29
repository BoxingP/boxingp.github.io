---
layout: post
title: "使用Octopress在Github上搭建个人博客"
date: 2015-03-29 20:08:49 +0800
comments: true
categories:
- Octopress
- Github
- Tool
---

在Github上像是写代码一样写博客有一段时间了，有必要把搭建博客的方法整理一下，方便更多的人DIY，享受一下“博客驱动开发”。

{% img /images/2015/logo.png %}

<!-- more -->

Octopress是[Brandon Mathis](http://brandonmathis.com/)在[Jekyll](http://github.com/mojombo/jekyll)上开发的，利用[Github Pages](http://pages.github.com/)来展示静态页面。

正如Octopress网站所说的

>Octopress is a blogging framework for hackers. You should be comfortable running shell commands and familiar with the basics of Git.

所以如果你不喜欢这种方式，可以选择其他博客平台或框架，毕竟工具就是为了让我们效率最大化而不是造成困惑。

本文是基于OS X系统进行介绍的。

##准备工作

1. 安装Git。

2. 安装Ruby 1.9.3。

在Mac上使用[brew](http://brew.sh/)安装Git：

```sh
$ brew install git
```

同样的，使用brew安装[rbenv](https://github.com/sstephenson/rbenv)之后，使用rbenv安装所需要的Ruby版本：

```sh
$ brew install rbenv
$ rbenv install 1.9.3-p194
$ rbenv local 1.9.3-p194
$ rbenv rehash
```

安装好后可以进行验证：
```sh
$ git version
$ rbenv version
```

##搭建博客

首先使用Git将Octopress从Github上clone到本地：

```sh
$ git clone git@github.com:imathis/octopress.git octopress
$ cd octopress
```

紧接着，安装依赖：

```sh
$ gem install bundler
$ rbenv rehash
$ bundle install
```

安装默认的Octopress主题：

```sh
$ rake install
```

##选择博客主题

有很多第三方的Octopress主题可供选择——[3rd Party Octopress Themes](https://github.com/imathis/octopress/wiki/3rd-Party-Octopress-Themes)。

通过git submodule add将需要的主题项目加为子模块，接着安装主题：

```sh
$ git submodule add GIT_URL .themes/THEME_NAME
$ rake install['THEME_NAME']
$ rake generate
```

我的博客使用的是`whiterspace`主题。

##配置博客

##部署博客

##开始写博客

（先挖坑，马上填）

**参考文献**

1. [Octopress](http://octopress.org/)

2. [利用Octopress搭建一个Github博客](http://beyondvincent.com/2013/08/03/2013-08-03-108-creating-a-github-blog-using-octopress/)

3. [Blogging With Octopress: Add Comments](http://asaf.github.io/blog/2013/07/08/blogging-with-octopress-add-comments/)

4. [Octopress添加回到顶部功能](http://droidyue.com/blog/2014/08/03/integrate-scroll-to-top-in-octopress/)

5. [3rd Party Octopress Themes](https://github.com/imathis/octopress/wiki/3rd-Party-Octopress-Themes)

6. [Markdown 语法说明](http://wowubuntu.com/markdown/index.html)