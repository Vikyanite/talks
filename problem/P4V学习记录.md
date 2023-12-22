# P4V学习记录

## 前言

最近不是要学UE之后写DS嘛，然后我们DS项目管理用的是P4（听说UE就是推荐用P4进行项目管理的）。对于只用过Git这种分布式VCS（Version Controll System）的我来说，P4这种集中化的VCS还是很有不同，于是就在此**记录**一下遇到的一些不了解的地方。

<u>PS: 只是纯纯小白就遇到所需要的功能的不懂之处与实现的记录，并非入门教学文章！</u>

## 学习过程中的好博客

[Perforce使用教程](https://zhuanlan.zhihu.com/p/45167236)

## 正文

关于怎么连接P4Server就不多了说了，一般的教程里面都有

### 1. Get Latest 会报Warning

warn的原因是你有修改的文件所以没办法GetLatest进行覆盖，所以<u>你要么就提交你的修改，要么就回滚你的修改</u>。

### 2. reconcile命令

最近大佬说如果P4经常出现更新不全的情况，导致出现打包失败或者cook失败，可以在根目录下使用`reconcile offline file`命令，然后对所有（按住ctrl+A全选）Modified文件右键选择`Get Revision`，然后在弹窗的Options中把第一项的**“Force xxx”**的勾选上。这样相当于就是<u>回滚你想要回滚的文件</u>了。

关于reconcile命令我找到一篇讲解的比较好的博客：https://www.shdsd.com/news-304/index.html

PS: 因为这个命令是**暴力**比较目录下的所有文件与服务器上的文件的区别，<u>所以目录如果很大的话会比较耗时，而且在选择所有文件的弹窗那里会有非常明显的卡顿，</u>所以如果没什么需要提交的的本地修改，建议直接全部`Revert`

### 3. revert命令

当有文件不想提交的时候可以选择revert，revert之后会放弃本地修改，从服务器重新拉取最新的文件。

选中文件或者Changelist之后右键，在弹出菜单里可以选择`Revert If Unchanged`或者`Revert`。Revert If Unchanged是当本地文件和服务器一样没修改的时候会回退。Revert则是强行回滚，放弃本地修改。如果选择的是Changelist，则会对Changelist下的所有问题做Revert操作。

### 4. Depot是什么

depot就是你服务器上的仓库

### 5. Workspace是什么

workspace跟git一样就是你本地的工作区

