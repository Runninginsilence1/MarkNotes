# 功能(需求)
![Img](./res/drawable/本科模拟答辩相关功能.png)

# Gradle 以及清单(Manifest)
```groovy
minSdkVersion 23
targetSdkVersion 29

dependencies {
    // 所有依赖优先考虑 新版本, 除非有兼容性问题
    // 这句话应该是 导入 lib包 里面所有的 *.jar文件, 感觉删了没问题
    implementation fileTree(include: ['*.jar'], dir: 'libs')
    // 基础导入 没的说 latest version: 1.3.0
    implementation 'androidx.appcompat:appcompat:1.0.2'
    // 约束布局的依赖, latest version: 2.0.4
    implementation 'com.android.support.constraint:constraint-layout:1.1.3'
    // material design latest version: 1.4.0
    implementation 'com.google.android.material:material:1.0.0'
    // Jetpack 导航图 Navigation
    implementation 'androidx.navigation:navigation-fragment:2.0.0'
    // Navigation UI
    implementation 'androidx.navigation:navigation-ui:2.0.0'
    // lifecycle 相关
    implementation 'androidx.lifecycle:lifecycle-extensions:2.0.0'
    // 牛魔臭斌, 怎么又来一个
    implementation 'androidx.constraintlayout:constraintlayout:1.1.3'
    // JUnit 没的说
    testImplementation 'junit:junit:4.12'
    androidTestImplementation 'androidx.test.ext:junit:1.1.0'
    androidTestImplementation 'androidx.test.espresso:espresso-core:3.1.1'
    // litepal 的依赖 重要! kotlin的版本 不一样, 这里需要另外去查
    implementation 'org.litepal.android:java:3.0.0'
    // ...
    implementation 'com.android.support:appcompat-v7:29.0.0'
    // RecyclerView 依赖
    implementation 'com.android.support:recyclerview-v7:29.0.0'
    // material design 的 卡片视图
    implementation 'com.android.support:cardview-v7:29.0.0'
    // 网络框架 okhttp的依赖
    implementation 'com.squareup.okhttp3:okhttp:3.4.1'
    // json解析 gson
    implementation 'com.google.code.gson:gson:2.7'
    // 图片框架 glide
    implementation 'com.github.bumptech.glide:glide:4.11.0'
    // glide相关 去查一下
    annotationProcessor 'com.github.bumptech.glide:compiler:4.11.0'
    // 不认识 去查一下
    implementation 'com.gyf.immersionbar:immersionbar:3.0.0'
    // not know
    implementation 'com.github.razerdp:BasePopup:2.2.1'
    implementation 'com.android.support:design:29.0.0'
    implementation 'de.hdodenhof:circleimageview:3.1.0'
    implementation 'com.github.lihangleo2:ShadowLayout:2.1.6'
    implementation 'me.grantland:autofittextview:0.2.+'
    implementation 'com.github.prolificinteractive:material-calendarview:1.4.3'
    implementation 'com.github.PhilJay:MPAndroidChart:v3.1.0'
    implementation 'android.arch.work:work-runtime:1.0.1'
    implementation 'com.github.chrisbanes:PhotoView:1.2.6'
    implementation 'cz.msebera.android:httpclient:4.4.1.1'
    implementation 'com.loopj.android:android-async-http:1.4.9'
}

```

# 源码分析
从类开始：
## BaseActivity：
这是一个抽象类，是用来提取公有功能的代码复用以及实现统一管理的。
继承了BaseActivity的A都拥有他的方法，并由一个集合进行统一管理。
欢迎页面的组成部分大概是背景图、中文、英文
中文是硬编码、英语用的api
里面还有很多工具方法，所以就不分析了。

## WelcomeActivity：
因为软件一开始会显示一个欢迎页面，因此这个软件的意图过滤器设置的WelcomeActivity为启动页面。别担心，MainActivity会出现的。
这个代码的分析我都写在代码里面了。
只需要明白这个A是干什么的并且它会怎么导航即可。
会根据你是否登录以及是否选择单词书来跳转到相关的页面。
用户完成这些后就会跳转到MainActivity中。

## MainActivity：
创建时：onCreate()流程：
执行init()进行初始化，
根据一个needFresh来判断是否执行一段动画。
最后初始化Fragment：initFragment()

三个封装方法的解析我都写在代码里面了。

Main就是APP三个主题功能Fragment的容器，里面只有一些初始化和处理Fragment切换的方法。
功能主要是靠Fragment实现的。

index包下面的三个就是三个用于切换的Fragment。
布局fragment_word.xml
整个就是如你所见的各种布局的组合。其实相当好看了。
总体的容器使用的是相对布局。

fragment内部的切换随记单词的方法setRandomWord()：
1. 传入书的ID，返回单词的个数int。
1. 通过random得到一个符合要求的wordId
1. 数据库查询得到 Word
1. 数据库查到 interpretation (解释)
1. 组合成字符串, 并更新UI.

fragment_word 中 的点击行为
一下复制于 我写在代码里面的注释
```java
            // 如果点击刷新按钮
            case R.id.img_refresh:
                // 刷新按钮的旋转动画
                // 这个跟我之前在B站上看的音乐播放的旋转特效是一样的。
                // 这只是个动画，不是核心功能

                // 也是一个刷新动画, 不清楚具体功能
                imgRefresh.startAnimation(animation);
                break;
                // 开始背单词的按钮, 点击会跳转到对应的页面去
            // 跳转到 LoadWordActivity(一个很美观的加载页面) 去
            case R.id.text_main_start:
                if (isOnClick) {
                    Intent mIntent = new Intent(getActivity(), LoadWordActivity.class);
                    // 以下的 flag的设置跟 android的启动机制有关, 暂时先不要花时间去弄这个
                    mIntent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
                    // 设置了一个转场的动画, 好吧我是没有看出来
                    startActivity(mIntent, ActivityOptions.makeSceneTransitionAnimation(getActivity()).toBundle());
                    // 原作者这样写的意思我还没搞清楚, 不过这里 将inOnClick 置为false 会导致如果 进入 背单词的A后用 back键立刻退出时,
                    // "开始背单词按钮会无法点击"
                    isOnClick = false;
                }

                // 这个按钮是 右上角的那个 flag一样的 显示当前日期的玩意
            // 实际点击的话是 类似欢迎页面的东西
            // 代码没有多长, 都是交给对应的 Activity来处理的
            case R.id.img_top_flag:
                // 单词的意思 区域, 设置的这个地方的点击事件啊
            case R.id.text_main_show_word_mean:
                // 引用传递给 WordDetailActivity的一个 static 成员.
                // 并准备跳转 对应的Activity
                WordDetailActivity.wordId = currentRandomId;

                // 右下角的搜索按钮, 挺明显的
            case R.id.card_main_search:
                // SearchActivity... 作者大大你是真的有心了 UI下的功夫很足
                // 和我前面写的注释差不多
                Intent intent2 = new Intent(getActivity(), SearchActivity.class);

              // 单词夹按钮
            // 同理
            case R.id.layout_main_words:
                Intent intent3 = new Intent(getActivity(), WordFolderActivity.class);

```

fragment_word 大概就结束了...

第二个Fragment: FragmentReview
UI的xml 我看了一下 design的标签, UI其实是死代码, 不过就是比死吗营销号的强上114514倍.

实际上 这个Fragment就是一个导航页面, 基本上所有的点击时间都是 Intent的跳转...

代码我过了一遍流程, 基本写成注释了

FragmentReview 大概就结束了...

FragmentMe的功能应该也差不多... 那么我暂时先不看了

现在回过头来... 大部分的Activity都是实际功能的代码了, 基本与架构无关

+ [ ] 我现在的问题大概是, 数据库的数据来源了...
+ [ ] 一个小 bug, 第一次点击 开始学单词之后然后马上退出来, 这个按钮会无法再次点击, 现在猜测和一个 field isOnClick 有关系.
+ [ ] 进度: LearnWordActivity

## ChooseWordActivity
布局很简单: 相对布局设置 顶部的 导航
然后就是一个 RecyclerView, 并且里面的item的点击事件是在 adapter 里面设置的
其他的就是具体的功能实现了... 所以我略过了

## LoadWordActivity
写的简单点: 
1. 绘制一个加载页面
1. 准备数据
1. 设置进度条的动画加载, 然后跳转到 LearnWordActivity

这个没仔细分析, 毕竟不重要

## LearnWordActivity
这个就是单词的学习的界面了, 这个 UI的设计可以好好学学
+ [ ] 可以抽个时间来看看 这个app的 UI设计, 我是真的很喜欢这种风格的软件

# 特定功能的实现
## 全局获取 Context
参考自 第一行代码, 并且背单词app的原生实现好像也是使用的这种方式(存疑)
很简单, Java定义一个静态的 Context, Kotlin定义一个 Compainon Object, 然后在onCreate()方法里面使用getAppicationContext()初始化.
然后, 需要让你的程序启动的时候使用自定义的 MyApplication类, 而不是默认的Application: 在 Manifest里面修改默认的 Application 为你的 Application即可.
此时, 直接 使用 MyApplication.context 即可获取上下文对象.
+ [x] ~~2022-08-06 15:05~~ 验证到底是不是这种方式获取的 Context

### android studio 提醒的 设置静态变量的内存泄漏的风险
这个提示是针对的 Context 这个类型的引用被设置成了静态变量
但是由于这里获取的不是Activity 或Service 中的Context，而是Application 中的Context，
它全局只会存在一份实例，并且在整个应用程序的生命周期内都不会回收，因此是不存在内存泄漏风险的。那么我们可以使用如下注解，让Android Studio 忽略上述警告提示：
```java
@SuppressLint("StaticFieldLeak")
```

## 复习一下 RecyclerView的用法
这个玩意可以说我是见识过很多次了, 不过我一直没有完全搞懂过, 看看今天我能不能完全搞懂.

一. 首先如果需要使用RView的话, 必须先在 布局文件中使用, 和其他的UI控件一样, 需要指定对应的宽和高, 这样他就只会在指定的区域显示.
二. 为 RView 准备适配器: 
> 适配器需要继承 RecyclerView.Adaper, 而且需要定义一个内部类xxxAdaper.ViewHolder, 并指定适配器的泛型为 这个内部类. 
> 内部类 ViewHolder需要继承 RecyclerView内部的 ViewHolder

以一个展示水果列表的适配器为例: 
```kotlin
class FruitAdapter(val fruitList: List<Fruit>) :
    RecyclerView.Adapter<FruitAdapter.ViewHolder>() {
    inner class ViewHolder(view: View) : RecyclerView.ViewHolder(view) {
        val fruitImage: ImageView = view.findViewById(R.id.fruitImage)
        val fruitName: TextView = view.findViewById(R.id.fruitName)
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
        val view = LayoutInflater.from(parent.context)
            .inflate(R.layout.fruit_item, parent, false)
        return ViewHolder(view)
    }

    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        val fruit = fruitList[position]
        holder.fruitImage.setImageResource(fruit.imageId)
        holder.fruitName.text = fruit.name
    }

    override fun getItemCount() = fruitList.size
}

```


关于这个类的解析: 
> ViewHolder: 顾名思义, 这个类是用来缓存 UI控件的**对象实例**的, 在这个类的内部定义你需要在Recycler里面需要用到的 UI控件.
> 它的主构造函数里面传入一个View参数, 通常是你需要的 UI控件的父元素布局, 这样就能通过 findViewById 访问他们的引用了.

> Adapter本身一般也需要传入参数, 一般是 **待展示的数据源**.

算了, 大概需要的时候我再去看, 基本就是模式代码, 没必要摸得那么清楚. 

