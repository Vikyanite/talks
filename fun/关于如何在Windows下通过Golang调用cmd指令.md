## 关于如何在Windows下通过Golang调用cmd指令

### 起因

目前我的项目中，当并发量提高的时候会出现UDP的buffer queue full的情况，我怀疑是因为UDP端口释放太慢导致堆积。

于是就打算用golang写一个命令行程序，每个0.5s执行以下cmd语句：

```powershell
netstat -an | grep "UDP"
```

### 过程

我记得golang是有一个exec库的，专门用来跑cmd，但是忘记在哪里看到了，就去网上搜。

首先是网上的教程大多是**linux**环境下的，所以他们的博客对与**windows**下的我**并不通用**。

这一点我试了很久...

#### One: 

使用网上类似这样的代码：

```go
func main() {
	c1 := exec.Command("netstat", "-an")
	c2 := exec.Command("grep", "UDP")
	c2.Stdin, _ = c1.StdoutPipe()
	c2.Stdout = os.Stdout
	_ = c2.Start()
	err := c1.Run()
	if err != nil {
		log.Panicln(err.Error())
	}
	_ = c2.Wait()
}
```

你就会收获如下错误

![image-20221108171457567](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2022-11-08-01ae31-image-20221108171457567.png)

就很懵。后来想想是因为`grep`是linux下的，之后就找到了windows下的代替品`find`

#### Two: 

之后代码就变成下面这样：

```go
func main() {
	c1 := exec.Command("find", "UDP")
	output, err := c1.CombinedOutput()
	if err != nil {
        fmt.Println(fmt.Sprint(err) + ": " + string(output))
		return
	}
	fmt.Println(string(output))
}
```

然而你运行之后会得到以下错误：

![image-20221108171723991](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2022-11-08-39ece3-image-20221108171723991.png)

？？？这是个什么错误，你倒是告诉我错误是啥啊

后来去stack overflow上找到了方法

https://stackoverflow.com/questions/18159704/how-to-debug-exit-status-1-error-when-running-exec-command-in-golang

简而言之就是，这个err会返回在`cmd.Stderr`上面，所以需要打印`cmd.Stderr`。或者调用`CombinedOutput()`。

#### Three:

但是你调用之后，会得到以下结果：

![image-20221108172045101](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2022-11-08-fe6505-image-20221108172045101.png)

？？？为什么会有乱码

查资料发现是因为cmd的输入输出都是遵循`GBK`编码的，但是中文是`UTF-8`编码的，所以中文就会显示乱码。

艹cmd是什么落后的东西啊。

之后去网上嫖了一个GBK转UTF-8的：

```go
func ConvertByte2String(byte []byte, charset Charset) string {
	var str string
	switch charset {
	case GB18030:
		var decodeBytes, _ = simplifiedchinese.GB18030.NewDecoder().Bytes(byte)
		str = string(decodeBytes)
	case UTF8:
		fallthrough
	default:
		str = string(byte)
	}
	return str
}
```

最后总算能显示中文了，但是显示的结果如下：

![image-20221108181539612](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2022-11-08-64f2b6-image-20221108181539612.png)

#### Four:

我看cmd都是这么用的怎么到这就拉垮了？

![image-20221108181703420](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2022-11-08-69e8bb-image-20221108181703420.png)

之后通过打印语句，看了一下以上的命令到底是什么：

![image-20221108181845077](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2022-11-08-ab4d2b-image-20221108181845077.png)

猜测可能是因为没有引号导致的，于是就加上引号，代码现在长这样：

```go
func main() {
	c1 := exec.Command("netstat", "-an")
	c2 := exec.Command("find", "\"UDP\"", "/c")
	c2.Stdin, _ = c1.StdoutPipe()
	c2.Stdout = os.Stdout
	_ = c2.Start()
	err := c1.Run()
	if err != nil {
		log.Panicln(err.Error())
	}
	_ = c2.Wait()
}
```

发现貌似卡住了，根本没输出。

#### Five:

debug模式进入之后发现是卡在了c2.Wait()这里，这个我也不知道为什么。于是就去网上换了一种方式，来写。

突然看到一篇博客说可以直接通过调用cmd.exe之后设置参数就可以直接一句话完成我最开始想要的cmd语句。

于是现在长这样：

```go
func main() {
	c1 := exec.Command("cmd.exe", "netstat -an | find /c \"UDP\"")
	output, err := c1.CombinedOutput()
	fmt.Println(c1.String())
	if err != nil {
		fmt.Println(fmt.Sprint(err) + ": " + ConvertByte2String(output, "GB18030"))
		return
	}
	fmt.Println(string(output))
}
```

结果报了这样的错误：

![image-20221108182412384](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2022-11-08-c5bca9-image-20221108182412384.png)

？我寻思这不就是字符串参数。这个奇奇怪怪的是什么？？

之后又尝试了这种写法：

```go
c1 := exec.Command("cmd.exe", "netstat -an | find /c UDP")
```

![image-20221108182516625](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2022-11-08-ed3b5b-image-20221108182516625.png)

结果还是会报错...

有可能是因为cmd的转义字符跟golang的有所不同导致的，于是我采用了raw string的形式

```go
c1 := exec.Command("cmd.exe", `netstat -an | find /c "UDP"`)
```

结果还是会有这样的错误...![image-20221108182721761](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2022-11-08-afa52e-image-20221108182721761.png)

```go
c1 := exec.Command("cmd.exe", `netstat -an | find /c UDP`)
```

![image-20221108182801939](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2022-11-08-630604-image-20221108182801939.png)

#### Six: 

就在我感到绝望的时候，我看到网上说powershell完美兼容cmd同时还会有更智能地识别，于是我就看怎么用golang调用powershell，发现只需要把之前的cmd改成powershell就可以：

```go
c1 := exec.Command("powershell.exe", `netstat -an | find /c UDP`)
```

![image-20221108183000344](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2022-11-08-747bb4-image-20221108183000344.png)

结果还是不对，之后我去网上搜：搜到了结果

https://blog.csdn.net/beebeeyoung/article/details/116677249

```go
c1 := exec.Command("powershell.exe", "netstat -an | find /c `\"UDP`\"")
```

![image-20221108183142533](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2022-11-08-fc1350-image-20221108183142533.png)

终于！！！！总算成功！！！！

完结撒花！！！

-------------------

#### 尾声

最后发现Prometheus地node-exporter里面自带监听端口的metric，所以只需要在grafana里面填上PromQL就可以（