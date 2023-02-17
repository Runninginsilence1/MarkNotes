# 求生之路2Linux开服
我分成三步
# 服务器的选择
这部分和开服本身没有太大的关系，毕竟服务器一有就直接上终端了，这里是**从未接触过服务器的我**对**云服务器和 Linux系统**的一个扫盲算是；

# 如何开服
参考[如何从0开始在linux平台上搭建求生之路服务器](https://www.bilibili.com/read/cv8467443)
我实际操作了，完成了一个最基本的搭服：**可以通过IP加端口连接进服务器**，管理员功能生效，但是tickrate插件没有生效；
大概的步骤是：
1. 下载 steamcmd需要的环境，还有软件 screen, 为了可以**断开shell连接后服务器依旧开启**
1. 下载cmd，然后使用匿名登录，**下载游戏的服务器客户端**。这样，纯净的原版游戏服务器搭建完成（但是**没有包含插件平台**。）
1. 服务端好了之后，需要**安装插件平台环境**，这样才能在服务器里面使用插件；
1. 修改一大堆游戏或者插件需要的配置，这样游戏才能够正常启动，或者说按你想要的方式去启动。
1. 游戏配置完成之后。。。你需要用一个软件 screen去启动游戏了，这样保证你可以shell离线后**服务器不关闭**

关于tickrate的插件我已经更新了

关于如果防止被ddos的话：
启动项里面加上`-nomaster`
## 具体操作
假设你已经连上了云服务器的终端；
### 库环境
对于Ubuntu：
```shell
sudo apt-get update   //更新apt-get，sudo用于临时提升权限，如果你有自信版本比较新，不用这个
// 并且小心使用更新指令，怕你需要更新的软件特别多，你都不好取消，怕出问题
apt-get install lib32gcc1  //安装依赖库，c++的
apt-get install screen   //安装screen
```
对于centos的，换成 yum，然后c++用这一句：`yum install glibc.i686`
还有一个命令：`yum install libstdc++.so.6`

具体看博客
[/lib/ld-linux.so.2: bad ELF interpreter解决](https://blog.csdn.net/l1028386804/article/details/77645925)

### 安装steamcmd的客户端
首先在你的用户根目录创建一个steam的文件夹：
`mkdir ~/Steam	//创建Steam文件夹`

在该目录下，下载linux版本的 steamcmd：
`wget http://media.steampowered.com/installer/steamcmd_linux.tar.gz`

下载完毕后解压压缩包
`tar -zxvf steamcmd_linux.tar.gz		//解压`

通过官方提供的sh来安装客户端：
`./steamcmd.sh`

等待安装完成，进入cmd的客户端:`Steam>`
执行匿名登录：`login anonymous`

强制待会安装求生之路2服务器客户端的目录位置为l4d2位置：
`force_install_dir ./Steam/steamapps/common/l4d2`
这个需要在匿名登录前执行。

然后下载求生之路2的服务端：`app_update 222860 validate`

下载完毕后即可退出 cmd： quit;

此时就已经可以通过命令启动服务器来测试一下试试了其实；



### 此时游戏


## 一些问题的解决
### 端口问题
Linux不需要重启！开服需要**放行对应的端口号**，因为默认云服务器提供者只会给你默认放行少量的端口；并且放行后还需要在本地（我指服务器的shell）里面修改对应的防火墙配置，双向放行对应的端口，而且求生是udp连接
方法看：[云服务器关于端口开放的坑](https://blog.csdn.net/qq_40855366/article/details/99011978)
`netstat -lntp` 查看当前端口占用情况

开放
```shell
iptables -I INPUT -m state --state NEW -m tcp -p tcp --dport 端口 -j ACCEPT
iptables -I INPUT -m state --state NEW -m udp -p udp --dport 端口 -j ACCEPT
 
# 删除防火墙规则，内容一样把 -I 换成 -D 就行了：
iptables -D INPUT -m state --state NEW -m tcp -p tcp --dport 端口 -j ACCEPT
iptables -D INPUT -m state --state NEW -m udp -p udp --dport 端口 -j ACCEPT
```
### 查看公网ip
连接游戏需要公网ip加端口连接
试试shell里面输入：`curl https://httpbin.org/ip`

### 多用户问题
和服务器有关的部分通常需要用户具有一部分超级用户权限，不过你不会想用root用户登录，可以尝试一下方式：
```shell
adduser steam
adduser steam sudo
login
```

# 选择想玩的插件，进阶就是自己编写插件了
写插件要有cpp的语法基础

玩转linux需要有一定的shell基础

# 服务器更新问题（情况很少见）
虽然情况很少见但是今天真就让我碰到了（铸币吧）
情况就是： 客户端更新，而服务器没有更新，两端的**游戏版本就不匹配**，服务器就相当于失效了。
服务器开服会显示：非最新版本，要求重启服务器来更新，但是其实**不用重启Linux服务器也可以的**

可以看这个博客：
[求生之路2服务器更新方法](https://www.bilibili.com/read/cv13455610)

其实打开Steam的cmd客户端，更新Server端就可以了。

`...your_Steam_cmd_path/steamcmd.sh` 启动Steam的cmd客户端；

2.设置安装目录(应该为left4dead2.exe所在目录
`force_install_dir ./left4dead2_ds/ //此处意为安装在一个名叫left4dead2_ds的在当前目录下的目录`

匿名登录 Steamcmd
`login anonymous//匿名登录，省事儿`

最后开始更新即可：
`app_update 222860 validate //更新求生之路2win服务器`

# 插件的切换
这里大部分的整合包的安装都是通过**覆盖文件的方式**来进行安装的。
所以如果要实现插件的切换的话，就是实现不同文件的覆盖...没办法...

原版： *_local
zonemod：*_zonemod
ast：*_ast

为了实现那个的话，每次都需要重新覆盖一遍
所以需要提前测试一下所谓的覆盖是怎么个覆盖法

原版，然后覆盖zonemod，然后覆盖ast

每次要进行切换的话，就需要删除原来的ast的文件，然后进行一次覆盖，

看看这三个东西对应的文件结构

原版的这个left4dead2的文件夹，它包括了游戏原本的内容，所以不适合整个复制下来，这会有一大堆的io操作，粒度还是得更加细致才行。

原版要备份：addons，cfg，scripts文件夹

AST的： 大小是
left4dead2文件夹内部就是addons，cfg，scripts这三个文件夹，还有一个motd的消息日志，可以现在游戏里面的

外部的话，就是日志和启动脚本。

zonemod的，它的压缩文件大概有63m的样子；
反正应该是要先安装zonemod然后再去弄ast了。




