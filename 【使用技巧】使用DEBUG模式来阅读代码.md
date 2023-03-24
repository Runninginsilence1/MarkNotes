# 【使用技巧】使用DEBUG模式来阅读代码
视频来源：[阅读代码的最佳姿势](https://www.bilibili.com/video/BV1824y1x7vD/)

文章通过三个例子来告诉你怎么高效的阅读代码。

**一个核心点：通过DEBUG模式来阅读源码**，而不是像你看漫画一样。

# 第一个例子
比如一个小型的函数，可以将断点打在那里，然后实际debug程序，这样debugger会在断点位置停下，你可以清晰的知道：
1. call stack，函数调用栈；
1. 参数的具体指；
1. 程序流程

这样更容易看出来一个函数是在做什么事。

如果一个函数是async的，那么通过并行debug（vs有这个功能，别的我不知道怎么弄出来）可以弄出多个thread（或者goroutine）的状态；

# 第二个例子
如果代码非常的复杂，比如阅读一段cpp的源码；一时间搞不清楚代码的位置；那么可以在必经的位置添加一个print函数或者别的什么，然后将断点打在此处，这样在debug的时候可以通过call stack看到调用顺序；这样就可以发现代码的位置了。

# 第三个例子
例如他这里是在阅读像是虚幻5引擎这样大型的源码：
如果是vs那就可以直接按f10来启动debug模式，这样vs会在启动函数那里停住，然后就可以查看启动流程了。

copy E:\Runninginsilence\Code\CSharp\AML2\AMLCore\bin\Debug\AMLCore.dll E:\Games\aml_pack\aml\core”
