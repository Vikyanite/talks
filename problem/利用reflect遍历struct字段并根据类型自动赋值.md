# 利用reflect遍历struct字段并根据类型自动赋值

> 让我们高喊”reflect牛逼！！“

早就知道reflect包很牛了，没想到这么牛。现在我在为我写的业务编写单元测试，在写测试途中发现，我的业务结构提非常大，如果我要生成的话会写一大串长的代码，而且还不能复用...对于一些简单的用例来说十分浪费。

于是就想能不能用reflect遍历struct字段，并识别他的类型给他自动赋值。没想到真的能实现，代码如下：

```go
func StructInit(a interface{}, i int) {
    // 这里因为传入指针，所以需要通过Elem()来获取实际指向的对象
   val := reflect.ValueOf(a).Elem()
   typ := reflect.TypeOf(a).Elem()

   for j := 0; j < typ.NumField(); j++ {
      field := val.Field(j)
       // 通过Type().Name()去识别类型
      switch field.Type().Name() {
      case "int64":
         dst := int64(i)
          // 通过Set去赋值
         field.Set(reflect.ValueOf(dst))
      case "[]int64":
         dst := []int64{int64(i)}
         field.Set(reflect.ValueOf(dst))
      case "[]string":
         dst := []string{strconv.Itoa(i)}
         field.Set(reflect.ValueOf(dst))
      case "string":
         dst := strconv.Itoa(i)
         field.Set(reflect.ValueOf(dst))
      case "int":
         dst := i
         field.Set(reflect.ValueOf(dst))
      case "time.Time":
         dst := time.Time{}
         field.Set(reflect.ValueOf(dst))
      }
   }
   fmt.Printf("%v\n", a)
}

func main() {
	for i := range TaskBOs {
        // 注意这里需要传入指针
		StructInit(&TaskBOs[i], i+1)
    }
}
```