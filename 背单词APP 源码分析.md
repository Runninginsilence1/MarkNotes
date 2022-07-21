# 功能(需求)
![Img](./res/drawable/本科模拟答辩相关功能.png)

# 源码分析
从类开始：
BaseActivity：
这是一个抽象类，是用来提取公有功能的代码复用以及实现统一管理的。
继承了BaseActivity的A都拥有他的方法，并由一个集合进行统一管理。
欢迎页面的组成部分大概是背景图、中文、英文
中文是硬编码、英语用的api
里面还有很多工具方法，所以就不分析了。

WelcomeActivity：
因为软件一开始会显示一个欢迎页面，因此这个软件的意图过滤器设置的WelcomeActivity为启动页面。别担心，MainActivity会出现的。
这个代码的分析我都写在代码里面了。
只需要明白这个A是干什么的并且它会怎么导航即可。
会根据你是否登录以及是否选择单词书来跳转到相关的页面。
用户完成这些后就会跳转到MainActivity中。

MainActivity：
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


