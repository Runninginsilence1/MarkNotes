# Rust编写的传输文件的软件
是的，这个就是留给我上班的时候看的。

首先代码的第1部分，嗯，它整个代码这里有两个模块，一个发送方，一个接收方嘛，对吧？

嗯，首先这里讲的是发送方的啊，所有的所有的逻辑都在一个文件中完成，无所谓，这只是个小程序。

然后讲解的顺序嘛，就是程序运行的那个流程，并非逻辑的流程，这个无所谓。

第1部分就是解析命令行的参数获得IP端口和文件路径。这个路径可以是文件的路径，也可以是文件夹的路径，并且可以同时输入多个非常方便。然后下面的一小段代码就是解析参数了，这个到时候看就可以了。

这里可以记录一下，就是说啊，发起TCP联机的那个代码段片段的位置。第1行语句就是发几个TCP连接给他提供IP和端口。然后构建一个缓冲区，用来缓存TCP连接的数据。那个数据的数据类型其实就是字节嘛，对二进制这种二进制数据啊，都是自己没有什么奇怪的，你就把它当成是某种数据来处理就可以了。
并且这个代码这里补充一下，我觉得应该就是它是创立了一个缓冲区，缓冲区里面还没数据呢，因为你前面那个拿的那个解析那里的文件啊什么的都还没开始嘛，这里只是先创建容器，到时候待会要用的，所以说还没有传进数据。

然后下面开始就是遍历文件，并且发送文件了。
这种风格其实我挺喜欢。
阿婆主也没有去每一行代码每一行代码去讲，主要就是讲那个思路。

他这个主要就是分两种情况文件的话就直接发送文件文件夹的话就递归去找文件夹里面的每个文件，然后再去调用发送的那个逻辑。

那发送方的过程逻辑大概就是这个样子啊，代码他也没有和也没有去讲。那我就到时候慢慢看呗，对吧，反正视频我也过了一遍了也没什么特别的东西。

嗯，然后的话我想想。
他这个接收方接收方的话，必须先跟发送方建立网络连接，就是说建立一个有效的TCP连接，不然他这里会直接报错的。

然后就从发送方那里接收到数据嘛，接收到数据，然后写入到硬盘里面。就很简单的一个思路，只是代码的话你不知道它具体怎么实现而已，我要看的就是这个，理论上来说，所有涉及到相同知识的编程语言都可以来做这件事，嗯，在看完Ross的这个之后呢，如果有时间的话可能会考虑一下练一些手。

你mlgb的，又开始讲代码逻辑了，给我气笑了。

发送方那里的代码讲了一点点，但是也没讲多久，那就不管了。

来看接收方这边的代码。就读取缓冲区里面的数据嘛，一个一个读，然后读到斜杠零那个位置的时候，它是一个结束符就代表数据已经读取完毕。这里好像是读那个描述信息吧，我操算了，无所谓。然后因为有斜杠0这个多余的数据嘛，他调用一个pop方法把它弹出去。

然后这里有一个type ID和一个相对路径，不管是文件还是文件夹都有这两个属性，所以先把它解析出来。哎哟，并且这玩意儿还是跨平台的，我操，他还可以给你区别是windows还是非windows的平台windows他那个文件描述符他就是那个字符串吧，它里面的那个磁盘的那个分隔符它是那个双斜杆，非常反人类。

他这个类型it它其实就是一个字符串啊，他没用状态码，那个无所谓，性能稍微会小，影响那么一点点，饿如果解析式文件夹那就去管文件夹吗？对吧？如果是文件的话就要去管文件。 是文件夹就创建一个文件夹啊，然后。
是文件的话就先解析文件的大小。然后计算一下能装满多少个缓冲区以及剩余的长度。

然后就创建一个空文件，并把接收到的数据写入到这个文件里面。然后最后的情况肯定是有一部分最后的一小部分数据装不满缓冲区，你还要去把这小部分数据也装进去，这样整个文件就算接收到了，我之前写过类似的，所以我清楚。 

接着就是演示这个程序喽。

他这个中端比较有意思，ls可以显示表格，然后输入秘密的时候也直接有明显的提示，厄评论区里面好像有说，我到时候准备直接看一下。

那这部分我就算看完了，到时候有问题直接把视频拿出来重新看一下吧，也不需要开声音。