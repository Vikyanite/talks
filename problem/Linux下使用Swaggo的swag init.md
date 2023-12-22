# Linux下使用Swaggo的swag init

## 前言

最近有个需求就是怎么在Linux下使用Swaggo的`swag init`语句，最后找到是需要将GOPATH添加到环境变量PATH里。之后线上跟本地环境不同，还需要做相对应环境下的不同代码适应。

## 过程

在windows下似乎只需要`exec.Command("cmd", "/c", "swag init")`，他就会**自动到有GOPATH的环境**执行语句。本地打开cmd运行`swag init`反而报了没有将GOPATH添加到环境变量的错误。

但在linux下面就需要把GOPATH添加到环境变量PATH中，于是就考虑如何在代码层面实现这个事情。也就是需求变成了：

- 在Windows环境下，执行`exec.Command("cmd", "/c", "swag init")`

- 在Linux环境下，将GOPATH添加到环境变量PATH，再`exec.Command("swag init")`

所以第一步就需要一段**能够判断go程序所在OS**的代码，[参考](https://blog.csdn.net/SaberJYang/article/details/128405150)：

```go
package main
import "runtime"
func main() {
   println(runtime.GOOS)
   if runtime.GOOS == "linux" {
      println("linux")
   } else if runtime.GOOS == "windows" {
      println("windows")
   }
}
```

难点的话其实是在Linux上，因为你需要往Linux环境变量中添加Go与GOPATH，同时也要注意避免重复添加。于是就考虑到了写一个shell脚本，脚本应该满足以下的要求（关于如何在Linux下添加环境变量的方式我已经在[这篇](https://www.cnblogs.com/Vikyanite/p/17215966.html#%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F%E9%85%8D%E7%BD%AE)提到过了）：

- 检查是否已经在`/etc/profile`里面添加过GOPATH与Go了
- 能够往`/etc/profile`中添加GOPATH与Go
- 重新加载环境变量

艹，学了半天总算给我写出来了（shell脚本好深奥啊），如下：

```bash
#!/bin/bash
#第一行是指定使用bash而不是shell

# 判断是否存在，不存在添加 Go 到环境变量
hasgo=$(grep "export PATH=\$PATH:/usr/lib/go/bin" /etc/profile)		#这里$PATH之后的记得换成你的go所在路径
hasgopath=$(grep "export PATH=\$(go env GOPATH)/bin:\$PATH" /etc/profile)

if [ "$hasgo" == "" ]; then
  echo "export PATH=\$PATH:/usr/lib/go/bin" | tee -a /etc/profile;	#同理
fi

# 判断是否存在，不存在添加 GOPATH 到环境变量
if [ "$hasgopath" == "" ]; then
  echo "export PATH=\$(go env GOPATH)/bin:\$PATH" | tee -a /etc/profile;
fi

# 重新加载环境变量
source /etc/profile

exit
```

之后就是windows与linux环境下，exec.Command执行不大相同，根据之前的代码组合修改一下：

```go
func prework() {
    if runtime.GOOS == "linux" {
        // 这里写上你自己的.sh路径
		execCommand("../swagger.sh")
	}
}

// 封装exec.Command添加不同系统对应的头
func execCommand(name string, arg ...string) error {
   if runtime.GOOS == "windows" {
      arg = append([]string{"/c", name}, arg...)
      name = "cmd"
   } else if runtime.GOOS == "linux" {
      arg = append([]string{"-c", name}, arg...)
      name = "bash"
   }
   cmd := exec.Command(name, arg...)
   cmd.Stdout = os.Stdout
   if err := cmd.Run(); err != nil {
      return err
   }
   return nil
}
```

之后就可以在代码层面运行跨平台的`swag init`啦~

## 参考资料：

[golang代码中调用Linux命令](https://www.jb51.net/article/275763.htm)

[linux中.sh文件是什么？怎么执行？](https://blog.csdn.net/Light_Travlling/article/details/103624680)

[shell脚本教程](https://blog.csdn.net/weixin_48006170/article/details/111830132)

[linux教程](https://www.runoob.com/linux/linux-tutorial.html)

[cmd /c和cmd /k 以及CMD命令](https://blog.csdn.net/lenny_wants/article/details/95357011)

[Linux中如何添加内容到文件末尾](https://baijiahao.baidu.com/s?id=1727019063436737118&wfr=spider&for=pc)
