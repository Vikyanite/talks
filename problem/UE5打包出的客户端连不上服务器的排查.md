# UE5打包出的客户端连不上服务器的排查

最近在打包DS项目时遇到了一个很奇怪的问题，就是Editor以Client模式起的客户端点击Button可以连接到服务器的Level

![image-20230704145726311](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-07-04-6336af-image-20230704145726311.png)

但是打包出来的Client点击Button却跳不过去

![image-20230704144750268](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-07-04-79de02-image-20230704144750268.png)

观察服务器日志发现有Error，但是光凭这条服务器的Error无法确定问题。

![image-20230704144936675](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-07-04-74440a-image-20230704144936675.png)

于是打开了Client的Log发现问题所在：

![image-20230704145122516](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-07-04-493b80-image-20230704145122516.png)

这个错误的意思就是说，在客户端找不到Server的Map，也就是说我们少打包了一个地图...

打开打包设置，果然如此。把打包设置的少的地图勾上后，客户端成功连接

![image-20230704145008009](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-07-04-f65e37-image-20230704145008009.png)

**完结撒花~**
