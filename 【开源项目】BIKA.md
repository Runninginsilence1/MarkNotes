# 添加下载的功能；
基本思路很简单：
从漫画信息ComicInfoActivity使用intent跳转到 ComicDownloadActivity
所以核心还是这个下载的逻辑要怎么写；

最好核心还是通过调试来过一遍代码的流程；看看数据从哪里来；

至少我有思路了...只是最近烦人的事情比较多，没办法，卡在这里了；



# ViewCompat
## gpt

Android中ViewCompat有什么作用？

ViewCompat是AndroidX库中的一个工具类，用于帮助开发者**兼容不同版本的Android平台**。它提供了一些静态方法，可以让开发者在不同版本的Android中使用相同的API来处理View组件。

例如，ViewCompat提供了一些方法用于控制View组件的可见性、背景和Padding等属性，在不同版本的Android中都能正确地工作。通过使用ViewCompat，开发者可以减少平台版本之间的差异，简化代码逻辑，提高开发效率。

总之，ViewCompat的作用是帮助开发者更方便地开发跨平台的Android应用程序。

一句话总结：就是通过一层封装，避免因为api发生变化后导致程序不可用；