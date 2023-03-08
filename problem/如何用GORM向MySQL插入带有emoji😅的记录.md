# 如何用GORM向MySQL插入带有emoji😅的记录

最开始因为我根据网上的GORM连接MySQL教程，所以在代码中charset参数是写死的，如下：

```go
fmt.Sprintf("%s:%s@tcp(%s)/%s?charset=utf8&parseTime=True&loc=Local", a, b, c, d)
```

但是现在前后端对接的时候，从前端传来的富文本中包含了emoji😅，就导致了GORM报错：

```go
Error 1366 (HY000): Incorrect string value: '\xF0\x9F\x92\xA2\xF0\x9F...' ...
```

经过排查发现是emoji的问题。



查阅资料发现：

> MySQL 5.7 及之前版本默认的字符集是 latin1 ，MySQL 8.0 版本默认的字符集是 utf8mb4 。不过使用 latin1 容易导致乱码，所以还是 utf8 和 utf8mb4 用途最广泛。utf8 其实是 utf8mb3 的别名，只使用 1～3 个字节表示字符。utf8mb4 使用 1～4 个字节表示字符，能够存储更多的 emoji 表情及任何新增的 Unicode 字符。utf8mb4 兼容 utf8 ，且比 utf8 能表示更多的字符，是 utf8 字符集的超集。所以现在一些新的业务建议将数据库的字符集设置为 utf8mb4 ，特别是有表情存储需求时。

原来是字符集的问题，uft8 = utf8mb3，所以我们需要显式指定utf8mb4才行。

-----

修改分为两步：

一、把数据库的字符集规则修改为utf8mb4

​	打开navicat 对你需要修改数据库右键->”编辑数据库“->"字符集"->选择uft8mb4即可，排序规则我这里没选

二、把GORM生成连接的字符集设置指定为utf8mb4

​	也就是把之前上面的中的`charset=utf8`改为`charset=utf8mb4`即可。