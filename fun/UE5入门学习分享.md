# UE5入门学习分享

> 按照我个人的学习经历来说，没什么比跟着教程做出一个Demo后再慢慢死磕代码更能让你入门

## 前情声明：

因为主要是为了**学习DS（ Dedicated Server 专属服务器）相关知识**，所以才入门UE5。所以本片博客的面向人群会是<u>没有接触过UE5，并且需要学习**DS相关知识**的人群</u>，而非更偏向以蓝图编程为主的偏客户端UI向的人群。

### 前置要求：

1. 有较为熟练的C++基础（模板、宏、虚函数、类等）
2. 环境已经配置完毕：UE5、VS2022（配备VA）

## 过程分享：

### 理解UE各个组成部分

分享一个从设计角度去更好地理解UE的设计理念与概念的文章：

> [《InsideUE4》](https://zhuanlan.zhihu.com/p/22813908)

感觉作者并不是以面向小白的方式来讲述UE4，而是需要有一定的使用经验与GamePlay框架的理解才可以结合这个系列来获得提升，所以我还找了一篇简洁明了一点的文章:

>[UE4学习笔记：Gameplay框架及其模块梳理（上篇）](https://www.cnblogs.com/u-n-owen/p/16425358.html)
>
>[UE4学习笔记：Gameplay框架及其模块梳理（下篇）](https://www.cnblogs.com/u-n-owen/p/16443936.html)
>
>[UE4网络编程（UE4++）](https://alexandrite.top/2020/05/20/ue4-wang-luo-bian-cheng-ue4/)

### 单人Demo

加入你要实现一个游戏，必然都是从单人版开始做起。群里的大佬周末花时间帮我们找了一个特别好的demo教程，这份教程很好的讲解了开发一个单人游戏所涉及到的各种需要的知识（美中不足的是没有游戏规则与各种关卡切换和打包的讲解）。

> [UE4C++快速上手_准备篇](https://www.bilibili.com/video/BV15f4y1U7ui)
>
> [UE4C++快速上手_实战篇上](https://www.bilibili.com/video/BV1BV411C7SH)
>
> [UE4C++快速上手_实战篇下](https://www.bilibili.com/video/BV16k4y167jH)

### 联机Demo

当你用Editor的ListenServer模式运行单人Demo时，会发现一些奇奇怪怪的bug，比如**一些生成物在一台客户端上有，但在另一台客户端上没有**。这就牵扯到UE的联机同步的知识了，所幸的是写了上面教程的大佬同时也有一份讲解联机的知识的视频：

>[彻底掌握UE4网络-01内容概要](https://www.bilibili.com/video/BV1dT4y1N7de)
>
>[彻底掌握UE4网络-02网络构架](https://www.bilibili.com/video/BV1Kv411471G)
>
>[彻底掌握UE4网络-03 Actor Replication](https://www.bilibili.com/video/BV1Up4y1x7KU)
>
>[彻底掌握UE4网络-04 Property Replication](https://www.bilibili.com/video/BV1tf4y1k7xc)
>
>[彻底掌握UE4网络-05 Rep_Notify](https://www.bilibili.com/video/BV1ht4y1z79w)
>
>[彻底掌握UE4网络-06 Ownership 所有权](https://www.bilibili.com/video/BV1T44y1k72n)
>
>[彻底掌握UE4网络-07 Actor Role](https://www.bilibili.com/video/BV1Vb4y1d7wV)
>
>[RPC第一部分错误纠正](https://www.bilibili.com/video/BV1Av411w7WT)
>
>[RPC第一部分](https://www.bilibili.com/video/BV1R34y1Q7b6)
>
>[RPC第二部分](https://www.bilibili.com/video/BV1Xq4y1N7qJ)

我很喜欢这个大佬的视频，不仅**声音很好听**，**英文的发音也很舒服**，然后不仅是知识的讲解还会带上实战，同时包括了**C++与蓝图两种实现方式**。可惜的是大佬比较忙，已经很久没更新视频了（

### 联机Demo加入AI敌人

在此前，我们的游戏都是只有玩家存在，但是一个游戏如果没有敌人或者NPC就略显乏味。于是我们就需要学习怎么给我们的游戏添加一点有趣的部分——AI。听着很高大上，其实也没有想的那么难：

>  [UE4蓝图开发生存游戏教程](https://www.bilibili.com/video/BV1yE411x7GD?p=54)

从P54开始到最后都是AI相关的知识讲解，因为感觉AI在UE中还是蓝图更为方便，所以就找了蓝图实现AI的方法。



\***未完待续~**\*
