# UE中各个对象的创建方法

转载自：[ Unreal Engine 4 C++ 创建对象的几种方法](https://blog.csdn.net/qq_20309931/article/details/52872965)

## **1.创建Actor对象**

创建Actor对象，需要使用UWorld::SpawnActor()接口，如下所示：

```cpp
/* <CreateObjectDemo> 
* 创建AActor派生类对象不要用NewObject或者new，而要用UWorld::SpawnActor() 
*/  
UWorld* World = GetWorld();  
FVector pos(150, 0, 20);  

AMyActor* MyActor = World->SpawnActor<AMyActor>(pos, FRotator::ZeroRotator);
```

------

## **2.创建组件**

在UE4中，为Actor创建组件，可以使用UObject::CreateDefaultSubobject()模板函数，这个函数只能在构造函数中调用。如下所示：

```cpp
/* <CreateObjectDemo> 
* 创建Component对象，要使用CreateDefaultSubobject模板函数 
*/  
MyComponent = CreateDefaultSubobject<UMyActorComponent>(TEXT("MyComponent"));  1234
```

**注意：这里有坑，TEXT(“MyComponent”)的名字不能重复！！**

------

## **3.加载资源对象**

在UE4中，项目中的所有资源文件，不要看做是文件，而要理解为“静态对象”：也就是对象序列化的产物。加载项目资源可以使用“UObject::StaticLoadObject()”函数，其中重要的参数为对象的Name，而不是文件路径。UE底层提供文件读取功能，无论资源文件是存储我独立的.uasset文件，还是存储到.PAK文件中，对于上层都不需要关心。

```cpp
/* <CreateObjectDemo> 
* 加载模型、贴图等对象，使用StaticLoadObject函数 
*/  
UStaticMesh* SM_Vase = Cast<UStaticMesh>(StaticLoadObject(UStaticMesh::StaticClass(),  
    NULL,  
    TEXT("/Game/Assets/StaticMeshes/SM_Vase"))  
    );  

StaticMeshComponent = CreateDefaultSubobject<UStaticMeshComponent>(TEXT("StaticMeshComponent"));  
StaticMeshComponent->SetStaticMesh(SM_Vase);12345678910
```

------

## **4.创建UObject对象**

如果你有UObject的派生类（非Actor、非ActorComponent），那你可以使用NewObject()模板函数来创建其实例对象。

```cpp
/* <CreateObjectDemo> 
 * 使用NewObject模板函数，来创建UObject派生类对象 
*/  
MyObject = NewObject<UMyObject>(); 
```