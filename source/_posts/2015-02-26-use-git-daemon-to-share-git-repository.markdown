---
layout: post
title: "使用git daemon来分享Git Repository"
date: 2015-02-26 20:04:37 +0800
comments: true
categories:
- Git
- Tool
---

今天遇到一个问题，怎么在两台电脑之前分享Git Repo。之前的做法是使用AirDrop传输，或者用Skype发送。今天了解到一种新的方法，git提供了`git daemon`，可以很方便地分享本地Git Repo的读写权限。

<!-- more -->

`git daemon`启动了一个TCP服务，通过`git://`协议分享Git Repo，默认的端口号是`9418`，它会监听该端口，提供服务。但是需要注意的是，这种方式是不安全的，通常只在安全的值得信赖的网络中使用。如果想要安全的解决方案，应该使用[SSH协议](http://git-scm.com/book/en/v2/Git-on-the-Server-The-Protocols#The-SSH-Protocol)。

步骤是：

1.进入想要分享的Project目录：

```sh
$ cd Project
```

2.clone裸仓库：

```sh
$ git clone --bare Project Project.git
```

完成后，git会在当前目录下创建一个名为Project.git的目录。

3.在Project.git目录下创建一个名为git-daemon-export-ok的文件：

```sh
$ touch cart.git/git-daemon-export-ok
```

这是为了让daemon知道只分享该Git Repo，也可以在运行`git daemon`时加上`--export-all`，这样所有的Git Repo都会被分享。

4.将Project.git目录移动到你想要分享的目录中：

```sh
$ mv Project.git /Shared
```

5.运行`git daemon`命令：

```sh
$ git daemon --base-path=/Shared
```

这样就可以clone本地的Git Repo了：

```sh
$ git clone git://{Local Machine IP Address}/Project.git
```

`-–base-path=`，所有的请求都是该path的相对链接，运行上面的`git clone`命令时，daemon会理解path为`/Shared/Project.git`。

通过上述步骤，只能通过`git://`协议获得clone的权限，如果需要获得push的权限，可以在运行`git daemon`命令时加上`--enable=receive-pack`，或者进入`Shared/Project.git`目录，输入：

```sh
$ cd Shared/Project.git
$ git config daemon.receivepack true
```

git会在当前目录下的config文件中添加以下配置：

```sh
[daemon]
    receivepack = true
```

这时就可以允许其他用户拥有push的权限。默认上该权限是关闭的，因为使用的协议并未进行验证，也就是说任何人可以push任何东西到Git Repo中，包括清除它。这意味着只能在一个封闭的局域网中设置它。

**参考文献**

1. [Git on the Server - Git Daemon](http://git-scm.com/book/en/v2/Git-on-the-Server-Git-Daemon)

2. [Git - git-daemon Documentation](http://git-scm.com/docs/git-daemon)

3. [Git daemon服务器架设指南](http://www.royee.net/archives/254)