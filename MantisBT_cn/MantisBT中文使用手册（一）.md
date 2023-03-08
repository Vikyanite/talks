# MantisBT中文使用手册（一）—— 关于MantisBT

## MantisBT是什么

MantisBT是一个基于Web的Bug Tracking系统，于2000年11月首次向公众开放。随着时间的推移，它已经成熟并获得了大量的人气，现在它已经成为最受欢迎的开源Bug Tracking系统之一MantisBT是用PHP开发的，支持多种数据库后端，包括MySQL、MS SQL和PostgreSQL。

MantisBT作为一个PHP脚本，可以运行在任何PHP支持的操作系统上，并支持上述其中一个DBMS。目前MantisBT支持在Windows、Linux、macOS和各种Unix操作系统上运行。

## 这本手册是面向谁的？

本手册针对的是公司中负责评估、安装和维护MantisBT的人员，也就是MantisBT的管理员。

## License

MantisBT是根据GNU通用公共许可证[GPL](https://www.gnu.org/copyleft/gpl.html)条款发布的。MantisBT可以自由使用和修改。只要你遵守[GPL](https://www.gnu.org/copyleft/gpl.html)的发行条款，它就可以自由地重新发布。

## 如何安装？

MantisBT可用于多个Linux发行版，包括。Debian, Ubuntu, Fedora, Gentoo, Frugalware和其他。因此，如果你正在运行Linux，首先要检查你的发行版是否有MantisBT的软件包。如果没有，或者该软件包不是最新的MantisBT版本，那么你可能想从[这里](https://mantisbt.org/download.php)直接下载。

对于Windows、macOS和其他操作系统，使用上面提供的链接来下载MantisBT。该下载是以tar.gz或zip格式压缩的。这两种格式都可以用[7-Zip](https://www.7-zip.org/)等工具进行解压（如果是Windows）。

请注意，在任何时候，通常有两个 "最新 "的MantisBT版本可供下载。一个是最新的生产版本（稳定版），另一个是最新的开发版本，可能是alpha或候选版本。不建议在生产中使用开发版本，特别是当它还处于alpha阶段时，除非管理员熟悉PHP，并且能够排除和修复任何可能出现的问题。

## 为什么叫Mantis?

当最初寻求这个项目的名称时，Ken遇到了一个每个程序员都会遇到的问题。什么是一个好名字？它必须是描述性的，独特的，而且不能太啰嗦。此外，有多种含义也是一个很好的提示。最后受Apache、Mozilla、Gnome等开源项目的启发，想出了Dragonfly（蜻蜓）和Mantis（螳螂）两个名字。因为Dragonfly已经是一个webmail包的名字，所以最后取名Mantis。

Mantis是主要以小虫(Bug)和其他昆虫为食的昆虫。它们在农业中非常受欢迎，因为它们会吃掉以农作物为食的昆虫。它们也是看起来非常优雅的生物。因此，我们有一个在多个方面都相当有特色和描述性的名字。BT后缀代表 "Bug Tracker"，将这个项目与螳螂这个词的一般用法区分开来。然而，随着时间的推移，该项目通常被称为Mantis。

## Mantis的发展历史

Kenzaburo Ito和一个朋友在写他们的个人项目时，需要一个Bug Tracker。在搜索好的、免费的BT时，他们并没有找到合适的，于是他们自己写了一个。在经过重构跟优化后，他们决定将他们写的这个BT通过GNU通用公共许可证（GPL）免费向公众提供——选择GPL的原因是他认为开发工具应该是廉价或免费的。

2002年，Jeroen Latour、Victor Boctor和Julian Fitzell加入Ken，成为MantisBT的管理员和核心开发团队。这标志着MantisBT的生命进入了一个新的时代，它现在是一个团队项目。

## 技术支持

有很多资源可以帮助解答关于你对MantisBT技术支持上的问题，比如：

- [论坛][https://mantisbt.org/forums/] - 论坛是获得MantisBT技术支持的最多的地方。首先在论坛上搜索你的问题，如果没有找到，就发起问题。

- [Gitter](https://gitter.im/mantisbt/mantisbt)是一个基于浏览器的在线聊天工具，主要取代了团队对IRC的使用。在主聊天室中，你可以与开发者和其他MantisBT用户进行实时讨论。Gitter支持所有的现代浏览器，也提供基于Android和iOS的客户端，以及一个[IRC桥][https://irc.gitter.im/]。

- [IRC](http://www.mantisbt.org/irc.php) - IRC频道不再非常活跃，因为开发者已经转而使用Gitter进行现场讨论；尽管如此，该频道仍然开放。有许多免费的IRC客户端。XChat（用于Linux）、[HexChat][http://hexchat.github.io/]、[IceChat][http:// www.icechat.net/]等等。你也可以使用[Web Chat][http://webchat.freenode.net/] 通过你的网络浏览器连接到IRC，当你在阻挡IRC端口的防火墙后面时，这可能也很有用。IRC频道的日志会被存档，并在[MantisBT][https://www.mantisbt.org/irclogs.php]上提供。

- [Wiki](https://mantisbt.org/wiki/doku.php/mantisbt:start) - MantisBT Wiki有与 "How To (recipes)"、FAQ、功能要求、插件等相关的信息。

- 搜索 - 找到问题的答案的一个好方法是通过你最喜欢的搜索引擎在所有MantisBT网站和互联网上进行搜索，例如[Google][https://www.google.com] 或[Bing][https://www.bing.com]。

  **注意** 

  关于技术支持不应直接发送给MantisBT的开发人员。此外，我们的[Issue](https://mantisbt.org/bugs/)界面是专门用于报告MantisBT的Bug，而不是关于技术支持的Bug。

## 如何获取MantisBT的最新资讯

有几种方法可以了解MantisBT的最新情况，包括：

- 我们向在我们的[官方网址](https://mantisbt.org/bugs)上注册的用户发送发布公告和重要更新。这个步骤需要进入官网，注册账号并验证电子邮件地址。这个账户也可以用来报告、监控和评论与MantisBT有关的问题。
- [MantisBT Blog](https://mantisbt.org/blog/)用于传达有关新版本、与MantisBT有关的主题等的公告。我们鼓励用户订阅RSS源，以了解那里发布的新文章。
- [Twitter](https://twitter.com/mantisbt)用于通知用户有关MantisBT开发的最新细节。我们鼓励Twitter用户关注"@mantisbt"。

## 版本迭代说明

我们的版本编号惯例遵循[Semantic Versioning](https://semver.org/)的准则。给出一个版本号Major.Minor.Patch和一个可选的后缀（例如：1.3.0-rc.1）。

- Major - 表示核心包中的一个非常大的变化。重写或主要的里程碑。不能向后兼容的API变化。
- Minor - 在向后兼容的情况下，引入新特性或功能上的重大变化。
- Patch - 错误修复、维护和安全发布。
- Suffix - 可选的，表示一个开发版本。
	- aN 或 alpha.N 表示 alpha 版本 
	- bN 或 beta.N 表示 beta 版本
	- rcN 或 rc.N 表示候选版本。
	- 没有后缀表示是稳定版