# UE5打包预设路径错误

在UE5中，设置完打包预设后点击运行发现报了错误。

![image-20230704113302145](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-07-04-e7736d-image-20230704113302145.png)

仔细观察发现是因为`ERROR: Project 'E:/../../Demo05.1/Demo0.uproject' does not exist`，也就是预设的路径不对。所以我们就只需要修改预设的项目路径即可：

![image-20230704114047455](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-07-04-3d1a15-image-20230704114047455.png)

然后选择你要打包项目的.uproject文件所在目录就好啦~