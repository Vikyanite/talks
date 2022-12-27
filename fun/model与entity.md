在阅读公司的大佬写的项目代码的时候，发现有些struct的命名是叫xxxEntity。

我非常困惑，因为他这个struct是在model.go里面的。

于是我就去搜索“Entity与Model到底是什么？”

按照我自己的理解的话，其实这两个东西在我看过来，都差不多。感觉就是一个struct，里面定义了一些数据一些方法，但这个会被持久层的对象所引用。

找到了一些文章：

[Entity和Model的不同-关于代码的数据层](http://tutuge.me/2015/03/01/Entity%E5%92%8CModel%E7%9A%84%E4%B8%8D%E5%90%8C-%E5%85%B3%E4%BA%8E%E4%BB%A3%E7%A0%81%E7%9A%84%E6%95%B0%E6%8D%AE%E5%B1%82/)

[DDD and MVC: Difference between 'Model' and 'Entity]('https://stackoverflow.com/questions/3029952/ddd-and-mvc-difference-between-model-and-entity)

[什么时候用Model，什么时候用Entity？](https://www.zhihu.com/question/25256772)

[what is difference between a Model and an Entity](https://stackoverflow.com/questions/8743995/what-is-difference-between-a-model-and-an-entity)

看了这些文章之后，感觉Model跟Entity确实还是有比较大的不同。

### Model vs. Entity

> **Entity**: An entity represents a single instance of your domain object saved into the database as a record. It has some attributes that we represent as columns in our tables.
>
> **Model**: A model typically represents a real world object that is related to the problem or domain space. In programming, we create classes to represent objects. These classes, known as models, have some properties and methods (defining objects behavior).



>model是一个模型，里面装了各种数据，将一个model传递给view，在试图页面就可以使用model里面的数据来呈现到页面上。
>
>而entity是实体，就是和数据表一一对应的，一个实体一张表。

个人感觉这两种说法更接近我的认知。

也就是说Entity是用于直接与数据库交互的struct，

而Model的话是更为上层所使用的strut，并不与数据库一一对应，同时里面还会拥有一些方法与跟数据库无关的变量。



> 就是说，Entity还是原本的概念，只不过Model是基于Entity的更高层的数据抽象与处理。

----

我好像讲了什么，又好像什么都没讲...