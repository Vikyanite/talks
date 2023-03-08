# Go如何模拟前端发送MultiForm类型信息

## 起因

最开始我是使用APIPost模拟前端发送消息的，但是到了需要发送多个文件的需求的时候就有点问题了。因为它不能多选文件...官方给出的解决办法是这样的：

https://qa.apipost.cn/q/89d67962d3022577

目前是按照以下方式传输的

![Apipost怎么传多文件？](https://img.cdn.apipost.cn/qa/f2b835d8331a1c2c.png)

> **update: 结果发现APIPost7已经支持了，我是小丑**

所以最后用PostMan选择多个上传了。但是！！因为这个URL每次都要权限验证，所以每次都要登录之后获取Token再去跑URL，非常麻烦！于是就考虑自己写HTTP Client去模拟用户行为。然后就去网上找，**Go的Http包怎么样才能模拟发送MultiForm请求**。

## 过程

网上大多都是这样子

[Go-post上传多文件和其它参数](https://blog.csdn.net/chentaoxie/article/details/81369491)：两个key名（我的需求是一个key名下面有两个文件，并且还要发送键值对

```go
func (c *PostGetController) sendPostFormFile(url string, filename string)(error) {
    body_buf := bytes.NewBufferString("")               
    body_writer := multipart.NewWriter(body_buf)        
    // boundary默认会提供一组随机数，也可以自己设置。
    body_writer.SetBoundary("Pp7Ye2EeWaFDdAY");   
    //boundary :=  body_writer.Boundary()

    // 1. 要上传的数据
    body_writer.WriteField("key1", "value-string1")
    body_writer.WriteField("key2", fmt.Sprintf("%d", 45))
    // 2. 内存中的文件1，FormFile1
     _, err := body_writer.CreateFormFile("filekey1", "filename.txt")
    if err != nil {
        fmt.Printf("创建FormFile1文件信息异常！", err)
        return err
    }
    f1_content := "内存中文件1的内容";
    body_buf.Write([]byte(f1_content))
    // 3. 读取文件
     _, errr := body_writer.CreateFormFile("filekey2", filename)
    if errr != nil {
        fmt.Printf("创建FormFile2文件信息异常！", err)
        return errr
    }
    fb2, err := ioutil.ReadFile(filename)
    if err != nil {
        fmt.Println("打开文件异常!", err)
        return err
    }
    body_buf.Write(fb2)
    // 结束整个消息body
    body_writer.Close();

    // 
    req_reader := io.MultiReader(body_buf)
    req, err := http.NewRequest("POST", url, req_reader)
    if err != nil {
       fmt.Printf("站点相机上传图片，创建上次请求异常！异常信息:", err)
       return err
    }
    // 添加Post头
    req.Header.Set("Connection", "close")
    req.Header.Set("Pragma", "no-cache")
    req.Header.Set("Content-Type", body_writer.FormDataContentType())
    req.ContentLength = int64(body_buf.Len())
    fmt.Printf("发送消息长度:", req.ContentLength)
    // 发送消息
    client := &http.Client{}
    resp, err := client.Do(req)
    defer resp.Body.Close()
    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
       fmt.Printf("读取回应消息异常:", err)
    }
    fmt.Printf("发送回应数据:",string(body))
    return nil
}
```

要么就是只有一个文件...这里就不放代码了，上面类似

## 解决方案

最后终于在一个不知名的第三方网站发现了[ Http Post 发送文件流案例 ](https://www.kandaoni.com/news/26875.html)

其实说白了，就是**Create两个名字相同的FormFile，写入就好了**，具体可以看到代码关键部分

```go
// key:file 里面放多个文件
// multipart/form-data 传多个文件
func postFormDataWithMultipartFile() {
	client := http.Client{}
	bodyBuf := &bytes.Buffer{}
	bodyWrite := multipart.NewWriter(bodyBuf)
	images := []string{"img.jpg", "img1.jpg"}
	for _, val := range images {
		file, err := os.Open("./images/" + val)
		defer file.Close()
		if err != nil {
			log.Println("err")
		}
        // !!!关键代码是这里
		fileWrite, err := bodyWrite.CreateFormFile("file", val)
		_, err = io.Copy(fileWrite, file)
		if err != nil {
			log.Println("err")
		}
	}
	bodyWrite.Close() //要关闭，会将w.w.boundary刷写到w.writer中
	// 创建请求
	req, err := http.NewRequest(http.MethodPost, imagePostURL, bodyBuf)
	if err != nil {
		log.Println("err")
	}
	// 设置头
	contentType := bodyWrite.FormDataContentType()
	req.Header.Set("Content-Type", contentType)
	resp, err := client.Do(req)
	if err != nil {
		log.Println("err")
	}
	defer resp.Body.Close()
	b, err := ioutil.ReadAll(resp.Body)
	if err != nil {
		log.Println("err")
	}
	fmt.Println(string(b))
}
```

