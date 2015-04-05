---
layout: post
title: "CNNIC证书值得信任吗？"
date: 2015-04-04 18:33:59 +0800
comments: true
categories:
- CNNIC
- Certificate
- Security
- Google
- Mozilla
---

要回答这个问题，我们可以按照`What → Why → How`的学习顺序来得到答案。

<!-- more -->

#What

###CNNIC是什么[^1]

CNNIC是中国互联网络信息中心[^2]（China Internet Network Information Center）的缩写，是经中华人民共和国国务院主管部门批准，于1997年6月3日成立的互联网管理和服务机构。中国互联网络信息中心成立伊始，由中国科学院主管；2014年末，改由[中央网络安全和信息化领导小组](http://en.wikipedia.org/wiki/Central_Leading_Group_for_Internet_Security_and_Informatization)办公室、国家互联网信息办公室主管。

在很多场合，中国互联网信息中心都代表中华人民共和国政府行使相应的职权。

####职责

- 担当“国家级的互联网络信息中心”之职

- 作为中国的顶级域名`.cn`和中文域名注册管理机构

- 运行、管理域名系统，维护域名数据库

- 选择域名注册服务机构

- 监督、管理域名注册服务机构的域名注册服务

- 代表中国大陆各互联网络单位，与世界上其他互联网络信息中心，作业务联系

####工作

- 发布《中国互联网络发展状况统计报告》

- 规范域名注册服务

####运行及管理

- 它的运行和管理工作，由中国科学院计算机网络信息中心承担

- 它在业务上受[工业和信息化部](http://en.wikipedia.org/wiki/Ministry_of_Industry_and_Information_Technology_of_the_People%27s_Republic_of_China)领导

- 它在行政上受中央网络安全和信息化领导小组办公室、国家互联网信息办公室领导

###CA

CA是数字证书认证机构（Certificate Authority）的缩写。它是负责发放和管理数字证书的权威机构，并作为电子商务交易中受信任的第三方，承担公钥体系中公钥的合法性检验的责任。

###数字证书

数字证书为实现双方安全通信提供电子认证。它的原理是当在认定一个人身份时，是通过信任的另外一个人来进行的，即CA。

证书之间也存在着信任关系，是可以嵌套的。只要信任链上的第一个证书，那后续的证书都可以被信任。

证书之间构成一个树形关系，证书都要依靠上一级的证书来证明自己，而处于最顶上树根位置的根证书（Root Certificate）是不需要被证明。一旦根证书出了问题，后果是相当严重的。

###HTTPS

HTTPS是超文本传输安全协议（Hypertext Transfer Protocol Secure）的缩写，是超文本传输协议和SSL/TLS的组合。它的主要思想是在不安全的网络上创建一安全信道，并在使用适当的加密套件和服务器证书被验证且被信任的情况下，对窃听和中间人攻击提供合理的防护。

HTTPS的信任是建立在数字证书认证机构发放的数字证书上的。

###中间人攻击[^3]

在密码学和计算机安全领域中，中间人攻击（Man-in-the-middle attack，通常缩写为MITM）是指攻击者与通讯的两端分别创建独立的联系，并交换其所收到的数据，使通讯的两端认为他们正在通过一个私密的连接与对方直接对话，但事实上整个会话都被攻击者完全控制。在中间人攻击中，攻击者可以拦截通讯双方的通话并插入新的内容。

#Why

正常情况下，访问一个网站是这样的：

{% img /images/2015/true_website.png 500 500 %}

一个安全的访问，没有受到监听。

然而愿望是美好的，现实是残酷的。如果伪造的证书来自CA，那么在一个网站被DNS劫持的同时，伪造的网站又提供了一个来自CA的证书，这时浏览器依旧认为这次访问是安全的，而用户就会被监听：

{% img /images/2015/fake_website.png 500 500 %}

CNNIC证书就是这样的一个证书，当你和一个网站建立安全连接时，如果使用的证书是CNNIC证书，浏览器会默认这个连接是安全的。

CNNIC的做事风格[^4]：

- CNNIC制作的上网辅助软件“中文上网官方版软件”，由于其强制安装和无法彻底卸载，一度被北京市网络行业协会列入10款流氓软件名单并勒令整改[^5]。为此，CNNIC起诉把它列为流氓软件的殺毒软件制作者[^6]。

- 2009年12月9日，CNNIC被中国中央电视台曝光对`.cn`域名管理不善，致使色情网站可以轻而易举的更换域名[^7]。12月11日晚，CNNIC发出公告[^8]，公告暗指从2009年12月14日9时起，禁止个人注册`.cn`域名[^9]。2010年1月底，CNNIC要求所有`.cn`域名提交个人身份证复印件，由于没有出台适当的隐私保护条例，遭`.cn`域名持有者抵制，不得已将个人身份证信息提交的最后的期限推迟到3月15日。但是提交信息的并不多。

最近，Google发现CNNIC颁发了多个针对Google域名的用于中间人攻击的证书[^10]。这个名为MCS集团的中级证书颁发机构发行了多个Google域名的假证书，而MCS集团的中级证书则来自中国的CNNIC。该证书冒充成受信任的Google的域名，被用于部署到网络防火墙中，用于劫持所有处于该防火墙后的HTTPS网络通信，而绕过浏览器警告。

根据最新的消息，Google和Mozilla已经决定在Chrome和Firefox中禁用CNNIC的证书[^11]。

如维基百科上所说：

>这件事情再次显示，互联网证书颁发机制公开透明的必要性。

#How

如何吊销CNNIC证书[^12]：

###OS X

- 打开`Keychain Access`

- 点击左栏的`System Roots`

- 找到`China Internet Network Information Center`和`CNNIC ROOT`双击

- 点击`Trust`，将属性全部改为`Never Trust`

###Windows

- `Win+R`运行`certlm.msc`

- 在左栏的`Trusted Root Certification Authorities`中的`Certificates`，找到`CNNIC ROOT`

- `Ctrl+C`复制

- 找到左栏的`Untrusted Certificates`

- `Ctrl+V`粘贴

###Firefox

Firefox的的证书体系是独立于操作系统的，所以在吊销了系统的证书后，还需要吊销它的证书：

- `⌘+,`打开`Preferences`

- 切换到`Advanced`，点击`Certificates`，点击`View Certificates`

- 切换到`Authorities`

- 里面的证书列表是按字母排序的，将`China Internet Network Information Center`和`CNNIC`之类的`Edit Trust`全部清空。

###全自动可疑证书吊销工具

这个是最方便的方法了，Github上的[RevokeChinaCerts](https://github.com/chengr28/RevokeChinaCerts)项目，可以按照步骤手动吊销。

###检验方法

打开[https://www1.cnnic.cn/](https://www1.cnnic.cn/)，请注意该链接是HTTPS，如果显示不安全，就表明操作成功：

{% img /images/2015/your_connection_is_not_private.png %}

##题外话

去年第一次知道关于CNNIC证书零零散散的事迹，当时就听到“如果利用证书建立虚假网站构建大局域网该怎么办”的担忧，于是迅速的ban掉了CNNIC证书。

知乎现在删除了大量有关CNNIC的讨论，曾经有一个问题[“CNNIC 证书值得信任吗？”](http://www.zhihu.com/question/20746900)，下面有一条回答是这样的：

{% img /images/2015/hit_face.jpg %}

脸打的好疼。

##参考资料

[^1]: [China Internet Network Information Center](http://en.wikipedia.org/wiki/China_Internet_Network_Information_Center)

[^2]: [中国互联网络信息中心](http://www.cnnic.net.cn/)

[^3]: [中间人攻击](https://en.wikipedia.org/wiki/Man-in-the-middle_attack)

[^4]: [CNNIC 干过的那些破事儿](http://program-think.blogspot.com/2010/02/about-cnnic.html)

[^5]: [十大流氓软件被勒令整改：3721居首位](http://www.pconline.com.cn/pcedu/soft/virus/da/0507/662204.html)

[^6]: [CNNIC正式起诉奇虎 奇虎列举CNNIC七大罪状](http://news.ccidnet.com/art/1032/20070124/1008413_1.html)

[^7]: [焦点访谈 失控的域名——聚焦手机网络色情（六）（2009.12.09）](http://space.tv.cctv.com/article/ARTI1260361552288770)

[^8]: [关于进一步加强域名注册信息审核工作的公告](https://www.cnnic.net.cn/html/Dir/2009/12/11/5749.htm)

[^9]: [CNNIC再发整治公告 暗指禁止个人注册CN域名](http://www.donews.com/Content/200912/6b5cd17b47284dea8526eb55e7dd5360.shtm)

[^10]: [Maintaining digital certificate security](http://googleonlinesecurity.blogspot.com/2015/03/maintaining-digital-certificate-security.html)

[^11]: [Distrusting New CNNIC Certificates](https://blog.mozilla.org/security/2015/04/02/distrusting-new-cnnic-certificates/)

[^12]: [CNNIC 证书的危害及各种清除方法](http://program-think.blogspot.com/2010/02/remove-cnnic-cert.html)