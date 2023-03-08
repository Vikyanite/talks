## 如何关闭gorm 1.20.0中的数据库实例

因为我没有在带有*gorm实例的Close()函数中找到

```javascript
dbURI := fmt.Sprintf("user=%s password=%s dbname=%s port=%s sslmode=%s TimeZone=%s",
    "username", "password", "dbname", "5432", "disable", "Asia/Kolkata")
fmt.Println(dbURI)
connection, err := gorm.Open(postgres.Open(dbURI), &gorm.Config{})

if err != nil {
    fmt.Println("Error connecting database")
    panic(err.Error())
} else {
    fmt.Println("Connected to database")
}
```

而且connection.Close()在GORM 1.20.0中已经不能使用了

----

所以就去百度了：

> GORM决定在1.20版中取消`Close()`方法，因为GORM支持连接池，因此正确的用法是打开连接并在应用程序中共享它。
>
> 如果您的特定用例仍然需要使用`Close()`方法，GORM提供了返回[db generic_interface](https://gorm.io/docs/generic_interface.html)的方法DB，您可以在其中使用它。
>
> 对于示例，
>
> ``` go
> sqlDB, err := db.DB()
> 
> // Close
> sqlDB.Close()
> ```