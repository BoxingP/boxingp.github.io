---
layout: post
title: "更好地配置git log"
date: 2015-01-26 18:45:06 +0800
comments: true
categories:
- Git
- Tool
---

[Git](http://git-scm.com/)是一个分布式的版本控制软件，由Linux内核开发者[Linus Torvalds](http://en.wikipedia.org/wiki/Linus_Torvalds)开发设计。这里多一句嘴，Linux和Git任意完成一个就可以留名青史，Linus一人包场了。~~妈妈问我为什么跪着写完这篇博客。~~

自己14年才开始使用Git，之前就是在本地很low地创建不同的文件夹来保存代码。使用Git后，~~“Diff，Add我闭着眼，reset回退你沉醉了没”~~可以很自由的对代码进行版本管理，而不用担心自己会丢失历史版本。

Git中使用`git log`查看之前提交的commit日志。

<!-- more -->

一般来说，在repo中输入`git log`后：

```sh
$ git log
```

默认是这样的，会新打开一个page显示如下的信息：

```sh
commit ef22c6836e1201035c94c73c8f6c258bdb461146
Author: BoxingP <boxingp@gmail.com>
Date:   Mon Jan 26 18:28:40 2015 +0800

    BLOG-6: add disqus comment in blog. Boxing

commit 32a27ef992743dff90db6ee925e6a680b1c3f995
Author: BoxingP <boxingp@gmail.com>
Date:   Mon Jan 26 17:32:44 2015 +0800

    BLOG-5: add google analytics in blog. Boxing

commit c089cec96eba70079152889a1f6a87be8893af48
Author: BoxingP <boxingp@gmail.com>
Date:   Mon Jan 26 17:04:54 2015 +0800

    BLOG-4: use solarized dark style. Boxing

commit 8011bdb7e814a8b43309116d0810d1e0ca8097c1
Author: BoxingP <boxingp@gmail.com>
Date:   Mon Jan 26 02:15:33 2015 +0800

    BLOG-3: update .gitignore. Boxing

:
```

`git log`会按提交时间列出所有的commits，最近的commit排在最上面。每次的commit都有一个SHA-1校验和、作者的名字和电子邮件地址、提交时间，最后缩进一个段落显示commit说明。信息很详细，但是在使用时显得很多，容易分散注意力。`git log`有很多选项可以帮助我们更好地工作，进行一定的配置后更加方便我们~~啪啪啪~~写代码。

经常使用的是`format`，可以定制要显示的记录格式，这样的输出便于后期编程提取分析，例如：
```sh
$ git log --pretty=format:"%h -%d %s (%cr) <%an>"
```

```
ef22c68 - (HEAD, origin/source, source) BLOG-6: add disqus comment in blog. Boxing (3 hours ago) <BoxingP>
32a27ef - BLOG-5: add google analytics in blog. Boxing (4 hours ago) <BoxingP>
c089cec - BLOG-4: use solarized dark style. Boxing (4 hours ago) <BoxingP>
8011bdb - BLOG-3: update .gitignore. Boxing (19 hours ago) <BoxingP>
:
```
下面列出了常用的格式占位符写法及其代表的意义：

| **选项** | **：说明** |
| :- | :- |
| `%H` | ：提交对象（commit）的完整哈希字串 |
| `%h` | ：提交对象（commit）的简短哈希字串 |
| `%T` | ：树对象（tree）的完整哈希字串 |
| `%t` | ：树对象（tree）的简短哈希字串 |
| `%P` | ：父对象（parent）的完整哈希字串 |
| `%p` | ：父对象（parent）的简短哈希字串 |
| `%an` | ：作者（author）的名字 |
| `%ae` | ：作者的电子邮件地址 |
| `%ad` | ：作者修订日期 |
| `%ar` | ：作者修订日期，按多久以前的方式显示 |
| `%cn` | ：提交者（committer）的名字 |
| `%ce` | ：提交者的电子邮件地址 |
| `%cd` | ：提交日期 |
| `%cr` | ：提交日期，按多久以前的方式显示 |
| `%d` | ：对应的branch分支名 |
| `%s` | ：提交说明 |

使用`format`时结合`--graph`选项，可以看到开头多出一些ASCII字符串表示的简单图形，形象地展示了每次提交所在的分支及其分化衍合情况：

```sh
$ git log --graph --pretty=format:"%h -%d %s (%cr) <%an>"
```

```sh
* ef22c68 - (HEAD, origin/source, source) BLOG-6: add disqus comment in blog. Boxing (4 hours ago) <BoxingP>
* 32a27ef - BLOG-5: add google analytics in blog. Boxing (5 hours ago) <BoxingP>
* c089cec - BLOG-4: use solarized dark style. Boxing (5 hours ago) <BoxingP>
* 8011bdb - BLOG-3: update .gitignore. Boxing (20 hours ago) <BoxingP>
:
```
下面列出了一些常用的选项及其释义：

| **选项** | **：说明** |
| :- | :- |
| `-p` | ：按补丁格式显示每个更新之间的差异 |
| `--word-diff` | ：按word diff格式显示差异 |
| `--stat` | ：显示每次更新的文件修改统计信息 |
| `--shortstat` | ：只显示--stat中最后的行数修改添加移除统计 |
| `--name-only` | ：仅在提交信息后显示已修改的文件清单 |
| `--name-status` | ：显示新增、修改、删除的文件清单 |
| `--abbrev-commit` | ：仅显示SHA-1的前几个字符，而非所有的40个字符 |
| `--relative-date` | ：使用较短的相对时间显示（比如，“2 weeks ago”） |
| `--graph` | ：显示ASCII图形表示的分支合并历史 |
| `--pretty` | ：使用其他格式显示历史提交信息，可用的选项包括oneline，short，full，fuller和format（后跟指定格式） |

更多的资料可以在[git-log](http://git-scm.com/docs/git-log)中查看。

如果每次在查看commit日志时都需要加上这些选项，会很麻烦。我们可以设置一个简单的alias。一般来说，上面的命令命名为`git lg`，当然叫什么取决个人喜好。在设置好后，输入`git lg`就会看到和之前一致的输出了。

```sh
$ git config --global alias.lg "log --graph --pretty=format:'%h -%d %s (%cr) <%an>'"
$ git lg
```
```
* ef22c68 - (HEAD, origin/source, source) BLOG-6: add disqus comment in blog. Boxing (4 hours ago) <BoxingP>
* 32a27ef - BLOG-5: add google analytics in blog. Boxing (5 hours ago) <BoxingP>
* c089cec - BLOG-4: use solarized dark style. Boxing (5 hours ago) <BoxingP>
* 8011bdb - BLOG-3: update .gitignore. Boxing (20 hours ago) <BoxingP>
:
```
我们可以看到每次的输出会在一个新的page显示，需要输入`q`才能退出。我们可以在设置alias时增加一条`--no-pager`的选项，让输出和输入的命令在同一page下显示。此时，可能commit的日志很多，可以增加一条`-6`的选项将显示的日志数量限制在6。
```sh
$ git config --global alias.lg "!git --no-pager log -6 --graph --pretty=format:'%h -%d %s (%cr) <%an>'"
$ git lg
* ef22c68 - (HEAD, origin/source, source) BLOG-6: add disqus comment in blog. Boxing (4 hours ago) <BoxingP>
* 32a27ef - BLOG-5: add google analytics in blog. Boxing (5 hours ago) <BoxingP>
* c089cec - BLOG-4: use solarized dark style. Boxing (6 hours ago) <BoxingP>
* 8011bdb - BLOG-3: update .gitignore. Boxing (20 hours ago) <BoxingP>
* b113f3f - BLOG-2: create a new blog about managing java versions with jenv. Boxing (21 hours ago) <BoxingP>
* 85d8807 - BLOG-1: init blog by using octopress. Boxing (2 days ago) <BoxingP>%
$
```
此外，你可以对输出结果的颜色进行设置，使输出结果更直观。毕竟工具就是为了让人可以将注意力更多关注到有产出的工作上，而不被其他东西分散。

**参考文献**

1. [Git官网](http://git-scm.com/)

2. [更好的git log](http://luolei.org/better-git-log/)



