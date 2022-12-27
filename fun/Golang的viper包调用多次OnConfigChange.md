# Golang的viper包调用多次OnConfigChange

目前由于项目中有热更新配置文件的需求，于是用上了viper库（[viper库用法介绍](https://learnku.com/articles/33908)）。

但在使用过程中发现，如果**采用记事本之外的IDE进行编辑会触发两次viper的回调**，查阅资料姑且找到了两种说法：

1. 公司的办公电脑安装了文件加密软件的原因 https://blog.csdn.net/wyongqing/article/details/126909977

2. IDE的写入方式与记事本不同 https://feijianshen.blog.csdn.net/article/details/112132729



*第一种解释我觉得不大靠谱，不过我公司应该是没有安装文件加密软件的，或者说是我的这部分是没有覆盖到的，不然不好解释我**IDE编辑与记事本编辑的情况不一致***

第二种情况通过debug模式，观察了一下viper的event的情况，如下：

#### 采用IDE编辑：

![image-20221214171541647](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2022-12-15-c3c2af-image-20221214171541647.png)

![image-20221214171547055](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2022-12-15-88f3df-image-20221214171547055.png)

![image-20221214171553360](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2022-12-15-3329bc-image-20221214171553360.png)

![image-20221214171558910](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2022-12-15-7f3ac2-image-20221214171558910.png)

![image-20221214171604853](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2022-12-15-e61eb2-image-20221214171604853.png)

![image-20221214171611951](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2022-12-15-745eaf-image-20221214171611951.png)

发现**触发了非常多的事件**，其中会调用回调的是**后面两个Write Event**。

#### 记事本：

![image-20221214171810014](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2022-12-15-b907c5-image-20221214171810014.png)

发现**只有一个write**。



笔者暂时还不知道为什么会有这样的区别，**我猜是IDE的文本修改的实现与记事本不同从而导致了这样的差异。**

### 解决方案

第二篇博客给出了一个解决方案，试了一下可行！在这里分享一下：

```golang
// GetMD5 获取byte对应MD5
func GetMD5(s []byte) string {
   m := md5.New()
   m.Write([]byte(s))
   return hex.EncodeToString(m.Sum(nil))
}

// 获取文件byte
func ReadFileMd5(sfile string) (string, error) {
   ssconfig, err := os.ReadFile(sfile)
   if err != nil {
      return "", err
   }
   return GetMD5(ssconfig), nil
}

func Init() {
	// 设置配置文件路径
	filepath := "xxxx"
	viper.SetConfigFile(filepath)

	// 获取文件MD5
	confMD5, err := ReadFileMd5(filepath)
	if err != nil {
		log.Fatal(err)
	}
	// 读取配置文件
	if err := viper.ReadInConfig(); err != nil {
		log.Fatal(err)
	}

	// 设置监控文件
	viper.WatchConfig()

	// 设置配置文件修改回调
	viper.OnConfigChange(func(e fsnotify.Event) {
		// 配置文件发生变更之后会调用的回调函数
		tconfMD5, err := ReadFileMd5(filepath)
		if err != nil {
			logger.Fatal(err)
		}
        // 比对当前MD5与之前是否相同
		if tconfMD5 == confMD5 {
			return
		}
		// 这说明文件发生了改变.
		confMD5 = tconfMD5
        
        log.Println("Config file changed!")
		
        // your hook func here
	})
}
```