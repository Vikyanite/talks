# UE5打包出现ERROR: Client target not found

由于需要测试Client连接DS来测试联机同步逻辑，所以需要打个Client的包出来，但是在我设置好Client的打包设置然后运行时，出现了**ERROR: Client target not found**

最后找UE官方论坛下的一个帖子：

https://forums.unrealengine.com/t/unrealfrontend-client-target-not-found/103129

原来是忘记加`Client.Target.cs`文件了....

你只需要复制你的项目文件下的`/Source/XXXEditor.Target.cs`文件，然后改名为`XXXClient.Target.cs`，然后右键编辑，把文本中出现`Editor`的地方都替换成`Client`即可。