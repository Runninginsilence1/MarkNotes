# Android ListView和RecyclerView
此教程用于回顾 ListView 和 RecyclerView的一系列用法.
因为此控件的使用即为广泛, 所以我有必要直接掌握它.
之前断断续续的学习导致我一直没能掌握, 这次我专门开了一个文档来记录它的使用方法.

通过第一行代码学习.

# ListView
## 简单的用法(想要在 Activity里面引入一个可供展示的列表应该怎么做)

在布局中加入ListView 控件,**定义好它展示的范围**, 以及在**引用它的时候所需要的ID**.

ListView 使用来展示大量数据, 所以在测试的时候先把数据准备好.

不过数据无法直接传入到 ListView 中, 所以需要一个适配器(Adapter). 

适配器(Adapter) 的作用就是负责将数据传入到 容器空间之中. 

适配器在安卓的底层有多种实现, 一般使用 ArrayAdapter.
```kotlin
val adapter = ArrayAdapter<String>(this,android.R.layout.simple_list_item_1,data)
```

关于这个适配器的实例化,  构造函数中依次传入Activity的实例、ListView 子项布局的id，以及数据源。注意，我们使用了
android.R.layout.simple_list_item_1作为ListView 子项布局的id，这是一个
Android 内置的布局文件，里面只有一个TextView ，可用于简单地显示一段文本。这样适配器对象就构建好了。

**总结一下**: 
1. 布局文件中定义好 ListView
1. 准备好数据源
1. 实例化 合适的Adapter, 做好初始化的工作, 然后将适配器与 容器(ListView)相关联.

可以看出 ListView 的运行有这么几个重要的部分: 
- 每个item的布局
- item进入屏幕和离开的屏幕时 ListView的渲染的问题;
- 每个item可能存在的单击事件怎么分别处理比较好

## 定制 ListView的页面
+ [x] ~~2022-08-20 16:51~~ 目前看来 ListView的所有自定义都是通过 它的适配器实现的...
> 点击事件单独由 ListView控制. 
### Adapter初始化
**item的布局自己定义即可**, 然后传入适配器.

重写一个构造器, 它的原型是 一个ArrayAdapter, 记住这个泛型在你定义的时候就已经固定死了, 如果你需要的话... 可以自己去设计泛型, 不过这不是我这次的重点.

并且调用 super()将对应的参数传入.

**总结**: 构造器接收三个参数: Activity的实例, item布局的id, 数据源data
并且在继承的构造器内传入, 完成初始化

### item滚动的渲染
这个重要的方法是getView()，这个方法在**每个子项被滚动到屏幕内**的时候会被调用。
也就是说他决定了 item的渲染的方式.**这里处理方式的不同也会严重影响性能.**

这里的一种通用的思路是: 
1. 使用inflate加载好item的布局
1. 通过 findViewById 这个方法获得里面的子控件的引用
1. 通过getItem这个方法获得 数据类的具体实例数据
1. 通过数据修改UI的数据
1. 返回视图


```kotlin
    override fun getView(position: Int, convertView: View?, parent: ViewGroup): View {
        // 通过inflate加载好UI
        val view = LayoutInflater.from(context).inflate(resourceId, parent, false)
        // 分别获得控件的引用
        val fruitImage: ImageView = view.findViewById(R.id.fruitImage)
        val fruitName: TextView = view.findViewById(R.id.fruitName)
        val fruit = getItem(position) // 获取当前项的Fruit实例
        if (fruit != null) {
            fruitImage.setImageResource(fruit.imageId)
            fruitName.text = fruit.name
        }
        return view
    }

```

+ [ ] 这里的 inflate方法的第三个参数需要设置成false, 看不懂它的解释
> 第三个参数指定成false，表示只让我们在父布局中声明的layout属性生效，但不会为这个
View 添加父布局。因为一旦View 有了父布局之后，它就不能再添加到ListView 中了。

可以看到, 修改 ListView 本质就是修改 item的布局. 

虽然是说 ListView的定制化, 不过整篇的篇幅几乎还是在 ListView的 Adapter上面.

## 提升 ListView的性能
### View的缓存
上面写的那个代码, 它的性能很差, 因为每次当item进入屏幕, 都会执行 getView这个方法, 而里面的逻辑是 重新加载View, 没有复用之前的缓存.
实际上这个方法是提供了缓存机制的, 就是他的参数 convertView

所以每次用一个条件判断 convertView是否存在, 存在复用即可. 

### UI控件的缓存
一样的, View的UI实例也是没有必要每次用 findViewById这个方法去获取的. 
一样的可以使用缓存的机制: 用了一个专门的内部类来实现这个功能: 通称 ViewHolder, 它是一个内部类, 里面直接存储 UI的实例

通过 view的tag属性可以将holder和view进行绑定

进行过完整优化的代码: 
```kotlin
class FruitAdapter(activity: Activity, val resourceId: Int, data: List<Fruit>) :
    ArrayAdapter<Fruit>(activity, resourceId, data) {
    // 定义内部类 ViewHolder, 用来作为 UI控件的封装
    inner class ViewHolder(val fruitImage: ImageView, val fruitName: TextView)
    override fun getView(position: Int, convertView: View?, parent: ViewGroup): View {
        val view: View
        val viewHolder: ViewHolder
        // 如果是首次加载布局
        if (convertView == null) {
            view = LayoutInflater.from(context).inflate(resourceId, parent, false)
            val fruitImage: ImageView = view.findViewById(R.id.fruitImage)
            val fruitName: TextView = view.findViewById(R.id.fruitName)
            // 将控件实例存入 ViewHolder
            viewHolder = ViewHolder(fruitImage, fruitName)
            // 并且通过tag进行绑定
            view.tag = viewHolder
        } else {
            view = convertView
            viewHolder = view.tag as ViewHolder
        }
        val fruit = getItem(position) // 获取当前项的Fruit实例
        if (fruit != null) {
            viewHolder.fruitImage.setImageResource(fruit.imageId)
            viewHolder.fruitName.text = fruit.name
        }
        return view
    }
}
```

**总结**: ListView的主要优化点就是缓存的问题

## 设置 item的点击事件
ListView的提供给用户的主要交互就是 点击的功能, 所以

**点击事件已经脱离Adapter**, 它是针对ListView进行的设置.

总之传入一个 Lambda表达式 或者一个匿名内部类(Java, 参数和前面一致)

解释一下参数: 
parent: 适配器的view, 应该有他自己的作用, 具体我也不知道
view: 具体是那个item的布局被点击了, 我猜应该跟监听的机制有关
position: 这个布局对应在adapter里面的位置, 可以在**业务逻辑里面帮助获取实例**
id: The row id of the item that was clicked.

然后在 Lambda表达式里面自定义具体的业务逻辑即可.

# RecyclerView: ListView 2.0
需要引入对应的 依赖才能使用 RecyclerView
implementation 'androidx.recyclerview:recyclerview:1.0.0'

## 实现与 ListView一样的效果
布局文件里面设置引入一个 recyclerview, 和 ListView是一样的.

**适配器的设置**
主类继承 RecyclerView.Adapter, 并且这个主类的泛型要指定成 待会要在类内部定义的一个 ViewHolder的一个内部类.

然后是这个内部类的定义, 需要继承 RecyclerView.ViewHolder, 功能和 ListView的 viewHolder是一样的. 
然后是 getView这个方法好像替换成了... onBindViewHolder()这个方法
onCreateViewHolder()方法是用于创建ViewHolder实例的. 内部的逻辑一般是:**使用inflate加载布局, 实例化并绑定布局, 然后返回**

onBindViewHolder()这个方法代替了 原先的 getView的方法, 在每次子项滚入到屏幕内部的时候调用. 
内部的逻辑一般是:**通过position得到对应的集合元素的实例, 然后更新UI的数据.**

另外新增了一个方法getItemCount(), 它用于告诉RecyclerView 一共有
多少子项，直接**返回数据源的长度**就可以了。

总结: 
估计 RecyclerView就是靠的 ViewHolder来提升性能的, 它的有关方法几乎都和 ViewHolder有关了.
大概的使用思路是: ViewHolder的实例化用来初始化UI控件; 创建的时候加载布局并实例化一个对应的holder对象, bind里面更新对应的逻辑. 

## More than ListView
## 实现横向滚动
ListView只能实现垂直滚动.
RecyclerView要设置横向滚动的话很简单, 得到一个 LinearLayoutManager的实例, 然后设置它的属性 orientation 就可以使得列表的方向为横向了.

**为什么RecyclerView 这么容易就实现这个功能了呢?**
ListView 的布局排列是由自身去管理的，而RecyclerView则将这个工作交给了LayoutManager。LayoutManager制定了一套可扩展的布局排列接口，
子类只要按照接口的规范来实现，就能定制出各种不同排列方式的布局了。

补充一个: layout的xml标签可以使用 tools简化, 引入tools的名称空间就可以了. 
## RecyclerView 的点击事件
RecyclerView 和 ListView不同, 没有提供类似 setOnItemClickListener()的方法来为 item注册监听事件, 而是 需要我们自己给子项具体的View去注册点击事件.

###  ListView 的设置点击事件的不足
ListView 的点击事件的设置基于 item本身, 但是item本身也是一个 ViewGroup 容器, 如果里面还有对应的按钮的话, 
ListView的机制就会变得不方便.

让我用我的话来说吧: 
ListView的每个item不是有一个布局吗?
ListView就是为这一整个布局设置的监听, 而如果这个布局内部也有业务逻辑的话, 就会变得不方便.

### RecyclerView 内部设置监听
RecyclerView 直接摒弃了子项点击事件的监听器，让所有的点击事件都由具体的View 去注册，就再没有这个困扰了。

在 onCreateViewHolder 里面设置监听: 
```kotlin
class FruitAdapter(val fruitList: List<Fruit>) :
RecyclerView.Adapter<FruitAdapter.ViewHolder>() {
...
override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
val view = LayoutInflater.from(parent.context)
.inflate(R.layout.fruit_item, parent, false)
val viewHolder = ViewHolder(view)
viewHolder.itemView.setOnClickListener {
val position = viewHolder.adapterPosition
val fruit = fruitList[position]
Toast.makeText(parent.context, "you clicked view ${fruit.name}",
Toast.LENGTH_SHORT).show()
}
viewHolder.fruitImage.setOnClickListener {
val position = viewHolder.adapterPosition
val fruit = fruitList[position]
Toast.makeText(parent.context, "you clicked image ${fruit.name}",
Toast.LENGTH_SHORT).show()
}
return viewHolder
}
...
}
```

其中的 viewHolder.itemView 就是对应的item最外层的布局, 给它设置监听事件 就类似 ListView的那个方法了.

+ [ ] 还剩下 横向的具体实现以及瀑布流的实践, 不过我想已经不影响我看代码了.