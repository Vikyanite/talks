# 如何在GORM GEN 中使用JSON Array(JSON_CONTAINS)

点开GORM GEN的[官方文档](https://gorm.io/zh_CN/gen/)，发现文档中对于JSON Query的描述就一句话

![image-20230216110017914](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-02-16-2838ed-image-20230216110017914.png)

我起初还以为是GEN不够发达，所以没实现JSON Array跟JSON Set

但是在[GORM的文档](https://gorm.io/zh_CN/docs/data_types.html#自定义数据类型集合)中却存在对于JSON的SET与Array的描述，说这些自定义数据类型定义在https://github.com/go-gorm/datatypes下。


-----

点开JSONQuery结构体，我们发现这个结构体的定义正好就是在go-gorm/datatypes下：

![image-20230216111006829](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-02-16-740105-image-20230216111006829.png)

也就是我们想要的JSONArray所在的地方，但是发现这个库文件并没有我们想要的JSONArray。最后发现原来是因为我的go.mod设置的`gorm.io/datatypes`版本不够，于是就去go.mod里面改：

![image-20230216111355215](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-02-16-3ffb58-image-20230216111355215.png)

之后`go mod tidy`一下就行了

------

然后我们就发现datatypes下有了JSONArray！

![image-20230216111850259](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-02-16-ac95fc-image-20230216111850259.png)

然后浅浅地调用一下就行了~

`query.X.Where(gen.Cond(datatypes.JSONArrayQuery("column_name").Contains("element"))...).Find()`

----

完结撒花！