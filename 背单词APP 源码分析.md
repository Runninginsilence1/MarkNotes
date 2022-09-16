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
+ [ ] LearnWordActivity页面的那个更新单词进度部分的代码看看是怎么实现的

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
在主页点击了开始背单词之后会进入的页面: 
功能大概是: 先复习单词, 然后学习新单词.

+ [ ] 写了一点注释, 不过需要对应的

## 这个 A依赖一个对应的 WordController
WordController是 Word 这个实体类的控制器，所以

## Word
这个是用于存储进入关系型数据库的实体类的数据，简单说就是对应的ORM映射。
介绍一下字段：就是说每一个单词应该有：
ID：唯一约束，但是我不知道 sqlite的主键规则，不知道他是不是物理层面的主键；
word：单词本身的string
ukPhone：英国音标，string
usPhone：美国音标，string
。。。
因为这些字段太长我不可能一个一个去看的，毕竟并不是我自己写的，所以拷贝了一下代码，注释很清晰

顺便还给它（每个单词）绑定了一个深度掌握的功能：也写在注释里面了，所以在需要的时候我再去看
```java
public class Word extends LitePalSupport {

    // ID
    @Column(unique = true)
    private int wordId;

    // 单词
    private String word;

    // 英国音标
    private String ukPhone;

    // 美国音标
    private String usPhone;

    // 巧记
    private String remMethod;

    // 图片(网址)
    private String picAddress;

    // 自定义照片
    private byte[] picCustom;

    // 自定义备注
    private String remark;

    // 设置归属
    private String belongBook;

    // 是否收藏
    @Column(defaultValue = "0")
    private int isCollected;

    // 是否是简单词
    @Column(defaultValue = "0")
    private int isEasy;

    // 是否是刚学过
    @Column(defaultValue = "0")
    private int justLearned;

    /*
     * 以下是学习复习专用的
     */

    // 是否需要学习
    @Column(defaultValue = "0")
    private int isNeedLearned;

    // 需要学习的时间（以天为单位）
    private long needLearnDate;

    // 需要复习的时间（以天为单位）
    private long needReviewDate;

    // 是否学习过
    @Column(defaultValue = "0")
    private int isLearned;

    // 总计检验次数
    @Column(defaultValue = "0")
    private int examNum;

    // 总计检验答对次数
    @Column(defaultValue = "0")
    private int examRightNum;

    // 上次已掌握时间（时间戳）
    @Column(defaultValue = "0")
    private long lastMasterTime;

    // 上次复习的时间（时间戳）
    @Column(defaultValue = "0")
    private long lastReviewTime;

    // 掌握程度（总计10分）
    @Column(defaultValue = "0")
    private int masterDegree;

    // 深度掌握次数
    /*
     * 前提：掌握程度已达到10
     * 当深度次数为0时，记下次复习时间=上次已掌握时间+4天，若及时复习，更新上次已掌握时间
     * 当深度次数为1时，记下次复习时间=上次已掌握时间+3天，若及时复习，更新上次已掌握时间
     * 当深度次数为2时，记下次复习时间=上次已掌握时间+8天，若及时复习，更新上次已掌握时间
     * 当深度次数为3时，记已经完全掌握
     *
     * 检测哪些单词未及时深度复习：
     * 首先单词必须掌握程度=10，其次单词上次掌握的时间与现在的时间进行对比
     * （1）要是深度次数为0，且两者时间之差为大于4天，说明未深度复习
     * （2）要是深度次数为1，且两者时间之差为大于3天，说明未深度复习
     * （3）要是深度次数为2，且两者时间之差为大于8天，说明未深度复习
     * （#）若未及时深度复习，一律将其单词掌握程度-2（10→8）
     *
     * */
    @Column(defaultValue = "0")
    private int deepMasterTimes;

```


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




# 以前我写的东西，先暂时存在这里
# 背单词的APP源码分析

通过配置清单中的文件：WelcomeA是启动Activity，会在里面进行相对应的初始化判断，均通过会直接进入主页
这些代码我应该都已经分析过了

软件第一次启动时会要求授予权限...
WelcomeA的结构分析：
首先A本身继承了一个基础类型：BaseAc...

基类负责定义共同的方法以及所有的A的相关管理
#### onCreate方法：
1. 首先用内置的Log类打印日志，显示当前的A的名称：getClass().getSimpleName()

2. 进行了一个if判断：定义了一个ConfigData的类来进行应用的配置文件的缓存，后面再说( todo
调用getIsNight()方法判断当前是浅色模式还是深色模式：
ImmersionBar是一个支持安卓4.0以上的第三方库，可以将应用的通知栏设置成沉浸式：

3. 之后将该BA（主要是它的子类）加入到一个全局的Activity管理器中（由Util包中的ActivityCollector类实现），方便统一管理：这个工具类的源码到时候再说（ todo

4. 调用了一个方法：
        getWindow().setSoftInputMode(WindowManager.LayoutParams.SOFT_INPUT_ADJUST_PAN);
		注释为：防止输入法将正常布局顶上去
这是一个Activity自带的方法：具体细节到时候可以慢慢分析 todo

以上，这就是基类重写的onCreate()方法，概括一下就是：设置沉浸式状态栏；防止输入法影响布局，保证全局的Activity能被统一管理。

#### onDestroy()方法
很简单，保证加入ActivityCollector的移除逻辑即可。

#### 权限管理
Andr oid 6.0 及以上系统在使用危险权限时必须进行运行时权限处理：
相关方法：requestRunPermission、onRequestPermissionsResult（这个是教材里面介绍到了的方法）

requestRunPermission：这个方法是作者自己定义的：todo 
两个参数：String[] 申请哪些权限；PermissionListener 自定义的接口，方法是已授权和未授权？需要重写
下面的部分涉及到6.0之后的动态授权的相关方法调用：简而言之是：
ContextCompat.checkSelfPermission()方法传入权限名，得到的返回结果等于PackageManager.PERMISSION_GRANTED代表已授权，否则需要去申请权限，这里先说到这里；
使用if else语句判断用户是够授权，没有则加入	一个新集合中等待用户进行授权；等待数组中的权限全部判断完成；

下面的条件判断有没有未进行授权的权限，有则去进行授权...
否则代表全部都授权：然后mListener的onGranted方法调用，这里的流程大概是... todo
申请权限使用requestPermissions()方法，第一个参数要求是Activity 的实例；第二个参数
是一个String数组，我们把要申请的权限名放在数组中即可；第三个参数是请求码，只要是唯
一值就可以。

在完成权限获取后会回调方法：onRequestPermissionsResult() todo 回调方法的逻辑暂时忽略

#### 服务管理
下一条是一个静态方法：isServiceExisted
实例化一个活动管理器，然后获得当前所有服务的数据放入到集合中；
如果不大于0则返回false；
true的返回逻辑说实话没看懂，所以可以待会再说 todo
不过可以简单说一下：是需要服务名和方法传入的类名一致才会进行返回...


#### 数据监测
下一个还是静态方法：
prepareDailyData（）：
首先先获取当前的时间（代码实现细节不分析）
LitePal是在Github 上开源的一款Android数据库框架，具体细节暂时不分析 todo

返回一个泛型为DailyData的List，泛型一个数据模型（实体类？应该）

下面条件判断：
如果这个List为空，调用基类中的另一个方法：analyseJsonAndSave();
或者里面第一项[0]的某个属性为null，那也调用这个方法；

#### analyseJsonAndSave() 方法
暂时不分析，只知道数据监控会调用这个方法 todo

#### 其他方法
有三个和框架有关，暂时不分析！
还有一个noNight表示不支持夜间模式，暂不分析 todo


至此，基类分析完毕。主要负责为全局活动添加功能：权限管理、服务管理、数据监测


## 启动页 WelcomeA  这个A是负责渲染随机壁纸和激励语句的A
解析一下W的属性：handler
重写消息的处理方法：
似乎是处理欢迎页的壁纸和激励语句的方法... 暂时不分析 todo


# 0519 单词


