# 基于Docker的求生之路2服务器搭建
首先up主这里提到，如果在windows上安装多可的话，推荐按最好按那个WSL 2 BACKEND的文档来安装。

然后安装好多个之后就可以直接拉去进项运行了，因为我是在Linux上操作的，所以待会儿主要的讲解以Linux为主。他的dockerfilefile都已经开源。

要运行的话，好像直接通过run命令就可以了。

视频中直接把命令复制进终端的话会有报错。
第1个报错似乎是网络端口未开放需要重启网络还是怎么它，直接复制到谷歌里面，然后找到了stack overflow上面的答案。

按照视频里面的操作，如果你遇到了一模一样的问题，也就是出现了forbidden啊attempt啊port等单词的话。
以管理员模式启动power shell，然后，输入这两个命令
net stop hns
net start hns

直接用docker这么做当然是可行的，但这不是我此行的目的。我的核心是想用多客来实现多容器装载不同插件并实现一键切换

嗯，他提到了可以修改插件，应该是他那一个里面内置了多种插件，然后通过某个参数可以修改它，现在的话在命令行那个plugin的位置就可以修改插件，这只是题外话

他在github那里写了维基，有需要可以看一下

同时如果要对容器进行微调的话，你直接在容器内部修改，然后退回终端使用commit来提交，你对它的修改就行

记得要把他的tag添加上去哦

另外如果你需要在容器内部通过root权限来进行操作的话，你可以使用这个命令，本质上他就是docker给你提供了一个接口，让你可以以入的用户身份来登录
docker exec -it -u root 容器的id bash

其他问题的解决放在那个维基里面了，接下来里面只会有我的确搞不懂的东西，我就说一下

没讲其他东西了，没办法，只能自己钻研了。

不过这样的话那就无所谓多可爱了，他本质上还是通过文件复制的那个……嗯，但我觉得我的思路是可行的，而且肯定要更方便，你容器管理起来，当然比命令要方便得多

来看看他那个docker文件的大概思路：
里面混杂了shell编程和dockerfile的相关命令...
怎么弄这个效率高一点。。。

注意！！在通过 dockerfile构建文件的时候，每一行命令都会在镜像上新添加一层；所以过多的dockerfile使用的命令会导致容器臃肿；
所以如果你只是有要执行多个shell命令的需求的时候，只需要命令之间用 &&符号连接就好了。

```dockerfile

```
