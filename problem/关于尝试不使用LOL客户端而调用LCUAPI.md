关于尝试不使用LOL客户端而调用LCUAPI

## 前言

众所周知，LCUAPI是Riot提供给开发者**通过LOL客户端**去调用的接口，但是我在Github上面看到了这样一个项目：[fenglingyeyu/lsocks5: lol socks5 lcuapi ](https://github.com/fenglingyeyu/lsocks5)

作者在README.md中写道：

![image-20230822132611881](C:\Users\vic\AppData\Roaming\Typora\typora-user-images\image-20230822132611881.png)

我就很感兴趣。如果可以不通过游戏客户端去调用LCUAPI的话，那么相当于lol助手就与客户端解绑了，可以独立存在而不是作为一个插件了。

## 实践

不过我已经很久没写c++项目了，我们clone下源项目，打开其中的.sln文件，点击编译，发现有点问题报错信息如下：

``` shell
无法打开包括文件: “uv.h”: No such file or directory	lsocks5
```

"uv.h"这个文件我就没见过...百度得知这是一个`Libuv`的相关的头文件。

于是就去网上找教程，怎么安装`Libuv`，还好强大的互联网一下子就让我找到了VS2022版使用`Libuv`的教程[Libuv的安装及运行使用](https://blog.csdn.net/qq135595696/article/details/125435743)在这篇博客与GPT的帮助下

![image-20230822155413952](C:\Users\vic\AppData\Roaming\Typora\typora-user-images\image-20230822155413952.png)

我总算编译出来了一个程序，然后当我看他的文档调用时候，