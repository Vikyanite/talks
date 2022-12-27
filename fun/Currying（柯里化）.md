# Currying（柯里化）

今早在看技术文章的时候，看到了一篇《Currying In Programing》的文章。看标题我以为是什么方法叫curry（咖喱），点进去一看，居然要VIP？于是就去网上看了。

> 感觉自从VIP才能看文章的模式出来之后，免费的文章都已经很少了。我寻思写这些技术文章的不应该是本着互联网的分享精神去写的嘛？虽然也能理解，现在大家都想要混口饭吃，VIP也不需要多少钱，上个厕所的事件就能赚到了，但还是会有抵触心理。

好在网上相关资料比较多，也算是把我给讲明白了。

## What Is Currying

currying的命名来源：由[Christopher Strachey](https://zh.m.wikipedia.org/wiki/克里斯托弗·斯特雷奇)以逻辑学家[Haskell Brooks **Curry**](https://zh.m.wikipedia.org/wiki/哈斯凱爾·加里)命名的，尽管它是[Moses Schönfinkel](https://zh.m.wikipedia.org/w/index.php?title=Moses_Schönfinkel&action=edit&redlink=1)和[Friedrich Ludwig Gottlob Frege](https://zh.m.wikipedia.org/wiki/戈特洛布·弗雷格)发明的。

柯里化，用一句话解释就是，把**一个多参数的函数转化为单参数函数**的方法。

举个栗子：

```golang
// 一般写法
func add1(value1, value2 int) int {
    return value1 + value2
}

// currying
func add2(value int) func (int) int {
  return func (a int) int {
    return value + a
  }
}

fmt.Prinlnt(add1(5, 10))  // 15
fmt.Prinlnt(add2(5)(10))  // 15
```

## Why Currying

我们可以发现上面的例子，其实并没有体现出currying的subtle。currying的应用场景更多的是**定制化地函数生成器**。比如一个计算折扣后的函数（例子来源于参考资料）：

```golang
func getDiscountPrice(price, discount float64) float64 {
   return price * discount
}

func main() {
   fmt.Println(getDiscountPrice(100, 0.9))
}
```

但是应用之后发现，每次打折的时候一般都是全场打折，意味着**每次使用这个函数的时候都需要传入第二个参数**，而不是仅仅传入折扣。于是这个时候currying就派上了用场：

```golang
func getDiscountPrice(discount float64) func(float64) float64 {
   return func(price float64) float64 {
      return discount * price
   }
}

func main() {
   getDiscount := getDiscountPrice(0.9)
   fmt.Println(getDiscount(100))
}
```

## Currying In Golang

<u>当我们学习了一个技巧的时候，更应该的是思考什么时候能用上，而不是为了用而去使用，这样反而是本末倒置。一切的技巧与写法都是为了需求而服务的，tricks只是为了更方便，更简洁地去编写需求。</u>

因为我的主要技术栈是golang。显然currying在go这种**强类型语言**中，肯定会受限。在golang中可能最主要的应用场景应该是，我们知道golang中不定长参数是**只能放最后声明的并且只能有一个不定类型**的参数，于是我们通过currying就能比较好的解决这个问题：

```golang
func add(b ...int, a string) {
    // can only use ... with final parameter in list 
}
```

方案：

```golang
func generator(b ...int) func(a ...float64) float64 {
   return func(a ...float64) float64 {
      var ret float64
      for i := range b {
         ret += float64(b[i])
      }
      for i := range a {
         ret += a[i]
      }
      return ret
   }
}

func main() {
   fmt.Println(generator(1, 2, 3)(4, 5, 6))
}
```

可以发现golang的强类型声明确实很冗长，不如currying在JavaScript与Python中好用。

## 参考资料：

>[golang函数式编程之柯里化](https://blog.csdn.net/u012280989/article/details/104491513)
>
>[什么是柯里化？](https://blog.csdn.net/Crazypokerk_/article/details/97674338)
>
>[JavaScript 函数式编程技巧 - 柯里化](https://www.freecodecamp.org/chinese/news/javascript-functional-programming-tips-currying/)
>
>[柯里化（Currying）](https://zh.javascript.info/currying-partials)
>
>[[JS] Functional Programming and Currying](https://pjchender.dev/javascript/js-functional-programming-currying/)