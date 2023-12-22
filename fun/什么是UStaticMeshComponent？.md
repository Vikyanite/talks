# 什么是UStaticMeshComponent？

从《InsideUE4》的Component篇中可以得知，UE采用的是**Entity-Component**模型，也就是把<u>实体赋予功能模块来让实体获得功能</u>。

UStaticMeshComponent是Unreal Engine中用于渲染静态网格模型的组件。它是用来显示静态的三维模型，如建筑、道具、地形等。

具体而言，UStaticMeshComponent用于将一个静态网格模型（Static Mesh）附加到Actor上，并在游戏运行时渲染该模型。它提供了许多属性和功能，可以控制模型的位置、旋转、缩放、材质等，以及处理碰撞和物理模拟等方面。

使用UStaticMeshComponent，开发者可以在关卡中放置静态模型并设置其外观和行为。该组件可以与其他组件（如碰撞组件、光照组件等）结合使用，以创建复杂的场景和交互效果。

总结而言，UStaticMeshComponent是用于显示静态网格模型的组件，为开发者提供了在游戏中渲染和控制静态模型的功能。

<u>我的理解：简而言之，如果只是一个空的Actor的话，你是没有**“形状”**这个功能的，然后`UStaticMeshComponent`就是这个功能的组件。</u>

