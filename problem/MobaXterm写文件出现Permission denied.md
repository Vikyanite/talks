# MobaXterm写文件出现Permission denied

原因：Mobaxterm只有几个特定的文件夹有权限。修改文件权限即可。（下面操作要在root权限下操作）

> 比如想上传文件到text文件夹下面，在text目录下面，使用语句
>
> ```bash
> chmod [-R] 777 text
> ```
> 这里 -R 是可选的，使用了说明会把text下的每个文件都变成可修改可访问可执行


再重新试一下即可使用。 