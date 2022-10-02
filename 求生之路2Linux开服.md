# 求生之路2Linux开服
我分成三步
# 服务器的选择
这部分和开服本身没有太大的关系，毕竟服务器一有就直接上终端了，这里是**从未接触过服务器的我**对**云服务器和 Linux系统**的一个扫盲算是；

# 如何开服
参考[如何从0开始在linux平台上搭建求生之路服务器](https://www.bilibili.com/read/cv8467443)
我实际操作了，完成了一个最基本的搭服：**可以通过IP加端口连接进服务器**，管理员功能生效，但是tickrate插件没有生效；
大概的步骤是：
1. 下载 steamcmd需要的环境，还有软件 screen, 为了可以**断开shell连接后服务器依旧开启**
1. 下载cmd，然后使用匿名登录，**下载游戏的服务器客户端**
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
对于centos的，换成 yum，然后c++用这一句：yum install glibc.i686
具体看博客
[/lib/ld-linux.so.2: bad ELF interpreter解决](https://blog.csdn.net/l1028386804/article/details/77645925)

wait updating...

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

# 选择想玩的插件，进阶就是自己编写插件了
写插件要有cpp的语法基础

玩转linux需要有一定的shell基础

