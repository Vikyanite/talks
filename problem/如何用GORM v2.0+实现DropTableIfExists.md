# 如何用GORM v2.0+实现DropTableIfExists

## 前因

最近在写测试的时候每次都要自己手动清空之前测试的表，想起之前写SQL语句的时候都会有DropTableIfExists，于是就去百度，看看GORM有没有已经封装好了。

## 过程

![image-20230218105202489](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-02-18-4a0f19-image-20230218105202489.png)

![image-20230218105217406](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-02-18-2d1498-image-20230218105217406.png)

发现网上说，gorm.DB直接就有这个方法。然后我试了一下发现没有，可能是因为GORM v2.0取消了这个方法。

![image-20230218105237423](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-02-18-12331e-image-20230218105237423.png)

只能说都没有找到我满意的答案，最后甚至还是直接写SQL之后让GORM去Exec的？！

我寻思你用GORM不就是为了避免写SQL嘛...

最后觉得GORM这么强大不可能没有封装，于是就直接去GORM在GitHub上的Repo找了。

## 最后

![image-20230218105504313](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-02-18-d3f995-image-20230218105504313.png)

发现v2.0中，取消了DropTableIfExists，转而封装了一个Migrator对象，是实现了根据Model去删表的操作，也就是跟之前DropTableIfExists的用法是一样的。

只不过现在调用需要这样写：

``` go
DB.Migrator().DropTable(&your_model{})
```

------

完结撒花！

