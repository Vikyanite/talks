# 关于Gin如何在multipart*/*form-data请求下解析JSON数组

## 前言

众所周知，在Gin下，如果只是在multipart*/*form-data请求下解析JSON对象到结构体的话就比较简单。但是如果是要解析JSON数组到对应请求结构体呢？

## 正文

举个例子：

```go
type AddItem struct {
    ID            int                      `form:"-"`
	Images        []*multipart.FileHeader  `form:"images"`
	RelatedItems  []int                    `form:"relatedItems"`
	RelatedStores []int                    `form:"relatedStores"`
}
```

然后前端传来的数据是这样子：

```json
{
    "images": {<FileSteams1>},
    "images": {<FileSteams2>},
    "relatedItems": [1, 2, 3],
    "relatedStores": [4, 5, 6]
}
```

~~（别问为什么前端不像传images字段一样传递数组过来，问就是前端觉得麻烦，卑微后端在线听前端任意使唤）~~

然后当我们用APIPost模拟前端发送请求，就会发现解析失败了：

![image-20230703172731982](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-07-03-5d925c-image-20230703172731982.png)

**这是为什么呢？**

很简单，<u>因为你给RelatedIds定义的是`int`类型，而你传入的确实`'[1,2,3]'`的`string`类型，所以`ShouldBind`方法会将`RelatedIds`识别成`int`</u>

## 解决方案

当然强大的Gin自然有它的解决办法，那就是**定义一个结构体，然后以JSON的格式传入**就行，如下：

```golang
type AddItem struct {
	ID     int                     `form:"id"`
	Images []*multipart.FileHeader `form:"images"`
	Related Related 			   `form:"related"`
}

type Related struct {
	ItemIds  []int `json:"itemIds"`
	StoreIds []int `json:"storeIds"`
}
```

然后就发现可以传进去并且没有报bug啦~

![image-20230703190359980](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-07-03-ba1007-image-20230703190359980.png)

**完结撒花~**