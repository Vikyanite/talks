# 如何在Windows环境下将Go程序以Linux环境编译

## 前言

因为最近有需要将项目编译成二进制文件上传到线上服务器进行部署的需求，发现Windows已经无法像之前[切换GOOS](https://blog.csdn.net/astronautyi/article/details/77084707)来达到目的了，特此记录一下半天的成果。

## 方案

在一天的寻找中我找到了两个办法来达成目的

### 方法一 使用Goland自带连接WSL2选择Output目录进行编译

说来这个非常凑巧。原本我是打算搜索wsl2如何使用Goland的，结果浏览器第一个就给我跳出来官方的[Goland连接WSL2的教程](https://blog.jetbrains.com/go/2021/05/05/compile-and-run-go-code-using-wsl-2-and-goland/)...

> 至于为什么是WSL2呢，是因为Goland对于WSL 1是不支持Debug模式的，Goland没有Debug的话其实就是一个很普通的IDE罢了..所以这里统一采用WSL2

其实官方已经讲的非常清楚了，我大概翻译一下

#### Step 1

 打开Goland 点击`Runing Tools`的`Running Counfigurations`的下拉小三角，选择`Edit Configuration`

![image-20230314154958181](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-03-14-a2cd2c-image-20230314154958181.png)

#### Step 2

进入到`Run/Debug Configurations`界面之后，在Run on选项选择`Create New Targets`——`WSL...`

![image-20230314155256088](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-03-14-85af60-image-20230314155256088.png)

#### Step 3

选择你的WSL2的版本，之后点`Next`（如果没有安装WSL2的话可以看[这篇教程](https://zhuanlan.zhihu.com/p/337104547)

![image-20230314155546134](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-03-14-a5d105-image-20230314155546134.png)

#### Step 4

发现之前的图好像是GoTest的，不过前几步没啥不同，这里用Go Build继续讲解，接下来就选择你设置好的Target，选择`Output directory`之后OK即可。(这个目录是输出二进制编译后的文件的目录

![image-20230314161906029](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-03-14-2c261f-image-20230314161906029.png)

之后`SHIFT + F10`运行你刚设置好的Configuration就好啦~

可以发现文件已经在目录下了

![image-20230314162126788](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-03-14-8cf997-image-20230314162126788.png)

### 方法二 采用WSL2之后进入对应目录进行Go build

这个方法主要是麻烦在怎么在WSL2下使用Git与`go mod tidy`，关于这一点我特地又写了一篇[WSL2使用Git与go mod tidy](https://www.cnblogs.com/Vikyanite/p/17215420.html)

## 参考：

[Golang 在windows下编译Linux下可执行文件](https://blog.csdn.net/astronautyi/article/details/77084707)

[Compile and run Go code using WSL 2 and GoLand](https://blog.jetbrains.com/go/2021/05/05/compile-and-run-go-code-using-wsl-2-and-goland/)