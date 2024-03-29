# 当函数遇见传参 🎯
有位置传参、指定参数名传参和混合两种方式。
并且，拥有默认值的参数位置必须要在最后。这个应该是编译器的事情。

# 探索Enum的成员值 🌟
cs中可以通过enum来定义一个枚举类型。里面只包含几个固定的实例，并且它的默认值是int。然后它还支持其他的数据类型。
默认从这个值类型的初始值开始计数。并且，如果给第一个值指定具体值的话，他就会从这个数开始递增。


# 舞动接口的实现 💡
首先我感觉这个是一个语法糖。


首先这里是一个什么情况：
一个类要实现一个接口有两种方式：
一个是实现接口的全部方法，还有一种就是：在类中**显式的实现一个接口的方法。**

语法类似以下：
```cs
interface MyInterface
{
    void Foo();
}

// 声明类实现了接口
class MyClass : MyInterface
{
	// 但是在内部定义的时候，在函数名前面加上了接口的名字
    void MyInterface.Foo()
    {
        
    }
}

```

这种首先可以想到的就是多接口内部方法名重复的意思，不过这个不重要；

第二种就是方法的可见性：
如果这么实现接口方法，那么相当于对编译器强调：这个方法对类实例的外部是不可见的（直接实现接口需要定义pub关键字；），只有在将这个实现类转换为对应的接口类型的时候才可以调用它。

# 解密await的神奇之处 ✨
关键字await，实际上只有某一个变量实现了一个方法，它的返回值是Awaiter，就可以进行await。

基于CS强大的拓展方法的机制，下面这个代码，就是可以成立的：
