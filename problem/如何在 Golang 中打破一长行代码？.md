# 如何在 Golang 中打破一长行代码？

这是Stack Overflow上的一个问题，今天同事问我，我发现我也不知道。

## 原问题译文：

我不习惯看到超过 80 列的代码行。所以当我遇到这个时：

```go
err := database.QueryRow("select * from users where user_id=?", id).Scan(&ReadUser.ID, &ReadUser.Name, &ReadUser.First, &ReadUser.Last, &ReadUser.Email)
```

我试图打破它



但我得到

```go
 syntax error: unexpected \
```

我还尝试通过按回车键打破行并在最后放一个分号：

``` go
err := database.QueryRow("select * from users where user_id=?", id) 

.Scan(&ReadUser.ID, &ReadUser.Name, &ReadUser.First, &ReadUser.Last, &ReadUser.Email);
```

但我再次得到：

```go
syntax error: unexpected .
```

所以我想知道这样做的 golangic 方式是什么？

## 原帖解决方案：

最简单的方法是将运算符 (&nbsp;.) 留在第一行

``` go
err := database.QueryRow("select * from users where user_id=?", id).
Scan(&ReadUser.ID, &ReadUser.Name, &ReadUser.First, &ReadUser.Last, &ReadUser.Email)
```

