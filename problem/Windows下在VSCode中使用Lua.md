# Windows下在VSCode中使用Lua

## 前言

看了大多数博文都是旧版的Lua的安装教程，比较多的windows一键安装推荐的软件比如LuaForWindows、LuaDist与LuaRocks都已经很久没有维护了。

### LuaForWindows

截至博文发布的事件，Lua官网最新版本已经来到了Lua v5.4.2，而LuaForWindows的更新记录却停留在了v5.1.5-52。

![image-20230704170332762](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-07-04-062803-image-20230704170332762.png)

### LuaDist

官网的Downloads也是停留在了x86的机器版本...

![image-20230704170833552](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-07-04-6ece1d-image-20230704170833552.png)

### LuaRocks

这个东西在Linux上挺好用的，但是对Windows就没支持...（用WSL当我没说

## 正文

于是乎，千辛万苦总算找到了一篇不错的博客：https://blog.csdn.net/ChinarCSDN/article/details/78667262

以下转自上述博客。

### 1 进入Lua官网：[http://www.lua.org](http://www.lua.org/)——下载Lua

### 2 点击——下载/download

![image-20230704171239664](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-07-04-047e58-image-20230704171239664.png)

### 3 点击——获取一个二进制文件/get a binary

![image-20230704171256257](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-07-04-c6aec3-image-20230704171256257.png)

### 4 进入新界面，点击左侧的Download，选择自己需要的文件进行下载

![image-20230704171352138](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-07-04-370d00-image-20230704171352138.png)

![image-20230704171401099](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-07-04-2977f1-image-20230704171401099.png)

### 5 下载并解压

解压出来大概是这个样子

![image-20230704171517660](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-07-04-93baee-image-20230704171517660.png)

### 6 将luaXX.exe改名为lua.exe, 添加到环境变量

### 7 到cmd中输入"lua"测试是否添加成功

### 8 vscode中安装Lua Debugger、Code Runner、Lua三个插件

![image-20230704171747968](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-07-04-b38a4e-image-20230704171747968.png)

### 9 创建hello.lua测试是否能运行

![image-20230704171856248](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-07-04-5a61ec-image-20230704171856248.png)

完结撒花~