# [转]使用vscode创建vue文件时，自动生成vue文件模板

原文链接：https://blog.csdn.net/qq_45832807/article/details/105429613

在vscode中没有像hubilder一样，创建vue文件后就自动生成vue模板。

![image-20230412185936149](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-04-12-9aec62-image-20230412185936149.png)

但是我们可以自己创建`vue.json`文件自定义配置。
记录一下我自己配置的过程🤭

1.打开vscode，在菜单栏中选择 **文件 / 首选项 / 用户代码片段**
这时就会打开搜索栏

![image-20230412190004232](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-04-12-22e942-image-20230412190004232.png)

2.新建全局代码片段 / 输入vue.json

![image-20230412190013262](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-04-12-911c65-image-20230412190013262.png)

3.手动配置vue.json文件
将下面的代码，复制到创建的vue.json文件中，保存，然后就🆗了

在使用的时候，创建vue文件，然后输入vue，按tab键，或者回车键，就可以创建一个自定义模板的vue文件辣
```json
{
	// 打印输出
	"Print to console": {
		// 前缀  也就是用户输入的快捷键内容
		"prefix": "vue",
		// 输出内容
		"body": [
			"<!-- $0 -->",
			"<template>",
			"</template>",
			"",
			"<script>",
			"</script>",
			"",
			"<style>",
			"</style>",
			""
		],
		// 描述
		"description": "Log output to console"
	}
}
```

![image-20230412190116378](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-04-12-1957a3-image-20230412190116378.png)

