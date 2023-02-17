# Root的相关知识
安卓系统基于Linux内核，安卓的权限管理也跟Linux比较相似。  
对于默认的安卓系统来说，有三个层次的权限：  
第一种第三方软件权限，不介绍。  
第二种是用户的权限，例如设置锁屏密码呀这些。这些三方应用是做不到的。  
第三种是root用户。 几乎可以接管系统的所有管理；
  
# 安卓分区的概念
安卓系统内部包含多个分区：
## BOOT分区
BOOT分区包含操作系统的内核和虚拟内存。  
这个应该是最底层的。获取root的操作通常也是在这个分区进行。  
如果BOOT分区被擦除或者损坏，手机就不能正常启动，会卡在开机的第一个画面。通常是LOGO界面。  

## SYSTEM分区
System分区则包含操作系统和系统内置软件。  
如果这个分区损坏，则会卡在开机动画的地方。  
有些厂商会额外添加一个vendor分区，里面包含定制应用与库文件。  

## DATA分区
data分区就是数据分区。包含各种数据，不会影响手机的正常启动。  

## CACHE分区
cache是缓存区，方便你快速启动你常用的应用。擦除该分区不会影响正常使用。  

## recovery分区
recovery分区，恢复分区。类似于Windows pe，可以用来恢复和更新其他分区的内容。  

卡刷通常就是在这个recovery里面进行，相当于一个**小型的操作系统**。

## AB分区机制
现在Recovery分区可能没有。因为安卓在7.0之后引入了新的ab分区系统：相当于两个空间。 跟双系统原理很像，但不完全一样。  

安卓11开始开始有虚拟ab分区。 双分区比较麻烦，因为recovery被分到boot分区里面了。    
所以大多数时候不推荐用recovery来刷机，而是通过更加底层的fastboot来刷机。他类似于bios。  

## FASTBOOT
Fastboot通常配合电脑来刷机。下载安卓sdk之后，通过fas boot devices命令  

此外，安卓还提供了一种叫做adb的方式来调试手机。通过这种方式来操作手机不需要root权限。 很多好玩的操作也可以通过adb来实现。Adb的时候好像是Linux的普通shell用户。  
  
  
现代的手机系统可能是安卓10。开启root时，通常需要在开发者选项里面开启一个oem，解锁引导程序。当然也有可能没有。  
  
Root的第一步通常是解锁bootloader。  
品牌不同，解锁bootloader的方法也不同。

在解锁BootLoader之后就可以考虑开始进行root了。  

# 现在推荐的Root方法
Root方法一般有两种：  
## 第一种就是通过recovery卡刷
但是手机默认自带的recovery是不会允许你进行卡刷的，所以我们要通过电脑刷入第三方的recovery才行。
现在最知名的第三方recovery当属twrp（国外）

找到适配你手机的img镜像，连接电脑，在电脑上执行刷写命令。
把trwp线刷进你的手机。
一般没有问题的话就完成了。

然后进入recovery，就可以开始考虑root了。

遥想我当年很着迷于各种一键root软件。  
现在看来面具是一统江山了。  
把面具的那个包放到手机里面，然后通过recovery刷，就算刷上了。然后重新启动后把文件改成apk，就可以安装管理工具了。

## 方法2
前面那种的原理是：在安装完面具之后，面具帮你修补boot分区来获取root。
  
现在是不去下载三方recovery直接用官方提供的镜像，通过面具进行修补之后再手动刷回到你的手机之中。

wait edit