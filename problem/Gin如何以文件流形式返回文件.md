# Gin返回文件形式

前言：似乎在官方文档只看见了怎么上传，并没有看到怎么下载...

## 文件流形式（发送下载链接）

百度发现Gin早就有成熟的封装好的函数：

```go
package gin
// File writes the specified file into the body stream in an efficient way.
func (c *Context) File(filepath string)

// FileAttachment writes the specified file into the body stream in an efficient way
// On the client side, the file will typically be downloaded with the given filename
func (c *Context) FileAttachment(filepath, filename string)
```

甚至封装的非常好...你只需要传入文件路径就会以文件流形式写在response里面了

### 出现wrote more than the declared Content-Length

具体的解决办法我是参考这篇博客的：[golang gin下载文件](https://www.cnblogs.com/hi3254014978/p/16278873.html)

采用这个办法，完美解决

#### 指定需要传输的文件类型Content-Type 和 Content-Transfer-Encoding

```golang
c.Header("Content-Type", "application/octet-stream")
c.Header("Content-Disposition", "attachment; filename="+fileName) // 用来指定下载下来的文件名
c.Header("Content-Transfer-Encoding", "binary")
```

----

后来我又出现了这个问题，经过一步步debug之后发现，是因为我在调用c.FileAttachment之后，又使用了自己封装好的将业务状态代码写入Response，导致最终的Content-Length大于c.FileAttachment设置的，从而导致panic



## 静态文件形式

这个Gin的官网早有说明，分为三个方法：

```go
func main() {
	router := gin.Default()
	router.Static("/assets", "./assets")
	router.StaticFS("/more_static", http.Dir("my_file_system"))
	router.StaticFile("/favicon.ico", "./resources/favicon.ico")

	// 监听并在 0.0.0.0:8080 上启动服务
	router.Run(":8080")
}
```