---
layout: post
title: "使用Octopress在Github上搭建个人博客"
date: 2015-03-29 20:08:49 +0800
comments: true
categories:
- Octopress
- Github
- Blog
- Tool
---

在Github上像是写代码一样写博客有一段时间了，有必要把搭建博客的方法整理一下，方便更多的人DIY，享受一下“博客驱动开发”。

{% img /images/2015/logo.png %}

<!-- more -->

[Octopress](http://octopress.org/)是[Brandon Mathis](http://brandonmathis.com/)在[Jekyll](http://github.com/mojombo/jekyll)上开发的，利用[Github Pages](http://pages.github.com/)来展示静态页面。

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

通过`git submodule add`将需要的主题项目加为子模块，接着安装主题：

```sh
$ git submodule add GIT_URL .themes/THEME_NAME
$ rake install['THEME_NAME']
$ rake generate
```

我的博客使用的是`whiterspace`主题。

##配置博客

正如Octopress的作者所说的，大部分情况下只需要修改`Rakefile`和`_config.yml`就可以了。其中`Rakefile`是和部署有关的，除非你使用rsync，否则不需要动它。

在`_config.yml`中，需要对三个部分进行配置。

###主要配置

```sh
url:                # For rewriting urls for RSS, etc
title:              # Used in the header and title tags
subtitle:           # A description used in the header
author:             # Your name, for RSS, Copyright, Metadata
simple_search:      # Search engine for simple site search
description:        # A default meta description for your site
date_format:        # Format dates using Ruby's date strftime syntax
subscribe_rss:      # Url for your blog's feed, defauts to /atom.xml
subscribe_email:    # Url to subscribe by email (service required)
category_feeds:     # Enable per category RSS feeds (defaults to false in 2.1)
email:              # Email address for the RSS feed if you want it.
```

###Jekyll和Plugins

这是和Jekyll和Plugins有关的，详情见[Configuring Octopress](http://octopress.org/docs/configuring/)。

###第三方组件

这些第三方组件是包含在Octopress中的，只需要配置好就可以添加到自己的博客中。

- Github：在sidebar中列出你的Github Repo。

- Twitter：添加一个分享到Twitter的按钮。

- Google Plus One：设置分享到Google +1。

- Pinboard：在sidebar中分享你最近的[Pinboard](https://pinboard.in/)书签。

- Delicious：在sidebar中分享你最近的[Delicious](https://delicious.com/)书签。

- Disqus Comments：访问[Disqus](https://disqus.com/)，创建好账号登录后点击`Settings`，点击`Admin`，就可以为个人博客站点创建一个short name，把它添加到`_config.yml`中的`disqus_short_name`后面就可以在个人博客中使用Disqus进行评论。

- Google Analytics：在[Google Analytics](http://www.google.com/analytics/)中获取跟踪ID，把它添加到`_config.yml`中的`google_analytics_tracking_id`后面就可以使用Google Analytics对个人博客进行分析统计。

- Facebook：添加Facebook`like`按钮。

##部署博客

###使用Github User/Organization pages

Github的[Pages service](http://pages.github.com/)允许我们为自己的Repo创建展示页面。我们使用`http://USER_NAME.github.io`作为博客的地址，当然你也可以使用自己的域名（[怎么做](http://octopress.org/docs/deploying/github/#custom_domains)）。

首先，我们在Github上新建一个Repo，把它命名为`USER_NAME.github.io`，其中`USER_NAME`是你在Github上的用户名。这是为了把`master`branch作为web server，使用`http://USER_NAME.github.io`链接展示你的页面。也就是说你需要在`source`branch上进行工作，并把生成的内容push到`master`branch上。

如果你觉的这些好麻烦，没事，Octopress会用一个配置task来帮助你把它们做好：

```sh
$ rake setup_github_pages
```

这个rake task会问你要Github Repo的URL。把上面我们新建的Repo的SSH或者HTTPS URL复制到这里（e.g. git@github.com:USER_NAME/USER_NAME.github.io.git）。

接着，它会为你做以下这些：

- 存储你的Github Pages仓库URL。

- 把指向imathis/octopress的remote由`origin`重命名为`octopress`。

- 把你的Github Pages仓库作为默认的`origin`remote。

- 把`active`branch从`master`切换到`source`。

- 使你的博客URL与你的仓库一致。

- 在`_deploy`文件夹中设置一个`master`branch用来部署。

紧接着运行：

```sh
$ rake generate
$ rake deploy
```

`rake generate`会把`source`文件夹下面的markdown文件编译为html文件，并复制到`public`文件夹下，因此`public`下的结构跟`source`的一致，里边的内容为最终的静态页面。

`rake deploy`会将生成的静态页面复制到`_deploy`文件夹下并把它们添加到git，commit然后push到Github Pages仓库的`master`branch上。

最后用浏览器打开`http://USER_NAME.github.io`，你就会看见自己的博客了。首次push可能会花费一段时间等待Github为你生成页面。

当然不要忘记把更改的文件push到`source`branch上：

```sh
$ git add .
$ git commit -m 'YOUR_MESSAGE'
$ git push origin source
```

###使用Github Project pages (gh-pages)

Github的Project Pages服务允许你为你的已存在的Repo提供一个站点。它会在你的Repo中寻找`gh-pages`branch，把上面的内容在这个链接中展示`http://USER_NAME.github.io/REPO_NAME`。

和上面步骤一致，只不过在运行`rake setup_github_pages`后输入的是已存在Repo的URL。

这个的好处是可以把`http://USER_NAME.github.io`留下来以后再使用，比如个人主页什么的。

##开始写博客

###博客

博客存储在`source/_posts`文件夹中，并按照Jekyll的惯例进行命名: `YYYY-MM-DD-post-title.markdown`。文件的名字会被用作URL的一部分，日期会用来对文件进行区分和排序。

Octopress提供一个rake task来生成一篇新的博客：

```sh
$ rake new_post["NEW_TITLE"]
```

创建好的文件默认是`.markdown`格式。

用编辑器打开一篇博客，开头的[Front Matter](http://jekyllrb.com/docs/frontmatter/)会告诉Jekyll如何处理博客和页面。

```sh
---
layout: post
title: "POST_TITLE"
date: POST_DATA
comments: true
external-url:
categories:
---
```

你可以关闭评论也可以为博客添加标签。如果还在打草稿，可以添加`published: false`以免它在生成文件时被发布。你可以添加一个标签也可以添加多个标签：

```sh
# One category
categories: Sass

# Multiple categories example 1
categories: [CSS3, Sass, Media Queries]

# Multiple categories example 2
categories:
- CSS3
- Sass
- Media Queries
```

###内容

使用Markdown语法（[语法说明](http://wowubuntu.com/markdown/index.html)）来写博客，也可以使用[liquid template features](https://github.com/Shopify/liquid/wiki/Liquid-for-Designers) 。

###生成和预览

```sh
$ rake generate   # Generates posts and pages into the public directory
$ rake watch      # Watches source/ and sass/ for changes and regenerates
$ rake preview    # Watches, and mounts a webserver at http://localhost:4000
```

`rake preview`是一个很好的功能，在发布博客之前可以先预览一下在页面的效果，进行修正。

##其他好玩的

###为博客添加返回页面顶部的功能

[Scroll To Top](http://www.scrolltotop.com/)这个网站提供了很多返回页面顶部的Widget。

在`source/_includes/custom`文件夹下新建一个`scroll_to_top.html`的HTML文件：

```sh
<script type="text/javascript" src="http://arrow.scrolltotop.com/YOUR_CHOICE.js"></script>
```

接着在`source/_layouts/default.html`中引入该文件：

```sh
{% include custom/scroll_to_top.html %}
```

此时博客在右下角添加了一个返回页面顶部的button。

当然也可以在`source/javascripts`文件夹下保存使用到的javascript文件，直接在`scroll_to_top.html`中调用：

```sh
<script type="text/javascript" src="/javascripts/FILE_NAME.js"></script>
```

制作属于自己的button。

###在首页添加“继续阅读”按钮

在博客中插入`<!-- more -->`后，在博客首页上，会把该标记之下的博客内容隐藏，点击`Read on →`按钮可以看到整篇博客。

###使用个性化的Favicon

在`source`文件夹下将`favicon.png`替换成自己的图片。注意，需要使用16px×16px的图片，256色或24位，所以图片的内容不要太复杂。

##小结

在Github上写博客可以分享知识给其他人，同时也是一个督促自己学习的好方法，毕竟搭建网站需要维护，而Github把这些替你做了，让自己可以专注注意力。当然也请善用，要善对这个平台。

在搭建博客时如果有什么问题可以问我，水平有限，会尽力解答。

**参考文献**

1. [Octopress](http://octopress.org/)

2. [利用Octopress搭建一个Github博客](http://beyondvincent.com/2013/08/03/2013-08-03-108-creating-a-github-blog-using-octopress/)

3. [Blogging With Octopress: Add Comments](http://asaf.github.io/blog/2013/07/08/blogging-with-octopress-add-comments/)

4. [Octopress添加回到顶部功能](http://droidyue.com/blog/2014/08/03/integrate-scroll-to-top-in-octopress/)

5. [3rd Party Octopress Themes](https://github.com/imathis/octopress/wiki/3rd-Party-Octopress-Themes)

6. [Markdown 语法说明](http://wowubuntu.com/markdown/index.html)