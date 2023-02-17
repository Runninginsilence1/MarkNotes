# ZoneMod 及 AST插件安装指南


# 视频的索引
00:14 ~ 04:40 云服务器购买以及远程SSH前的配置
04:46 ~ 10:09 通过XShell登录远程终端以及环境搭建
10:21 ~ 12:45 安装steamcmd以及l4d2软件
13:38 ~ 14:57 配置可以一键启动/重启/关闭 服务器的文件
15:09 ~ 17:18 下载ZoneMod相关的文件并通过覆盖文件的方式来安装ZoneMod插件
17:29 ~ 18:40 设置server.cfg来设置启动的配置
19:01 ~ 21:41 让启动文件生效，放行端口，复制ip进入游戏测试，大功告成！

# 流程
## 云服务器初始化和root账户设定
首先需要购买云服务器，因为ZoneMod只能在Linux服务器上运行，所以需要购买Linux发行版的镜像，一般是Ubuntu或者CentOS。
服务器的root先弄好。

在**能够通过ssh服务登录到远程**的话，就开始执行以下的命令：
这些命令的作用主要是安装steam或者是求生需要的一些库环境。

## 设置一个额外的账户来管理服务器软件
在安装之前先考虑创建一个新用户！
**不要用超级权限来管理服务器**。不仅没有必要，而且不安全。
可以命名一个用户叫做steam，让他能够运行root命令，这样你就不需要一直登录root来执行这些操作。

todo：考虑到这里来练习一下用户组的操作。

文件不要安装到root文件夹里面，安装到你新建的那个用户文件夹里面。创建多用户方便你管理服务器，尤其是在多服务器时候。

然后就照着命令把Steam的客户端和求生的服务器客户端都安装完。
安装完毕后，退出steam客户端。

下面的步骤是通过把文件加入到Linux服务器的init文件中，使得服务器可以开机自启，或者说通过指定的命令来一键启动，重启或者停止。这不是一个核心操作，不过我仍然打算来记录一下。

首先你需要弄到ZoneMod插件里面那个srcds1文件。
然后用可靠的文本编辑器打开它。
然后下面是关于这个文件的解释。

SRCDS_USER是指使用这个文件的用户，我不知道指定错误的话会有什么后果，不过在使用多服务器的时候，最好还是把它修改正确，我可能会尝试一下修改错误是什么后果。

IP和port要指定你的主机的外网IP和你想开放的端口。

PARAMS一般是你在启动软件之后，游戏要应用的参数。包括地图端口以及一些选项命令的。很容易理解。

修改这些内容就可以。
然后把这个文件上传至/etc/init.d文件夹

关于这个文件夹，可以非常粗略的理解为求生里面的autoexec文件夹，他是Linux启动时候的运行脚本的文件夹的软链接。总之放到这个里面的话，求生的服务器应该就会随服务器的启动开机自启。 我觉得暂时没有必要这么做。


下面开始准备正式安装ZoneMod
可以在自己的电脑上下载，然后远程推送到自己的服务器。
也可以通过wget指令来下载文件再解压。
这里贴一下命令。


现在ZoneMod算基本安装完成。
来对server.cfg做一下配置。
你可以把它重命名为1，这样他的名字更有标识度。
然后用可靠的文本编辑软件打开它。
有这么几个选项值得注意：
hostname：服务器的名字，不支持中文。可以加插件，使得它支持中文，我可能会弄弄

sv_tags hidden可以用来隐藏服务器刷新，但是不会影响服务器浏览器

sm_forcematch zonemod可以强制服务器在启动时默认为ZoneMod

sm_cvar mv_maxplayers 10可以设定服务器的最大加入人数。

接下来再给配置文件进行一下设置，就可以启动服务器了

首先给那个文件加入一个可运行的权限
sudo chmod +x /etc/init.d/srcds1

再然后去腾讯云的控制台开放一下需要的端口。我设置的话应该是36606
添加防火墙规则应该就可以。



# scrd1文件注释（文件放到/etc/init.d中）
SRCD_USER：对应的是你当前Linux用户的用户名
DIR：修改成你的游戏路径
IP && PORT：你的外网ip地址（应该是，**这里还没验证**）以及端口
PARAMS：这里是程序启动时的启动参数， 删除ip；修改初始地图；增加选项 -nomaster（服务器不公开，只能通过ip+端口直接连接）

# 通过github安装插件的话有两种方式
1. 直接下载压缩包，然后通过xftp直接上传，方便但是效率不高；
1. 在server的终端直接通过指令来直接下载

总之文件覆盖完成后，插件就算安装完场了

# 设置server.cfg
sm_forcematch zonemod可以设置启动服务器的时候就设置成zonemod；
总之一些服务器相关的配置都可以在这里进行修改；

然后开放端口就可以下载了其实；


# 求生之路2ast插件-readme
考虑自己来挖掘ast的src并将其运用到我的服务器中。

# 一些参考资料
[SourcePawn语言的一些基本语法（zh）](https://wiki.alliedmods.net/Zh_cn:Introduction_to_SourcePawn_1.7)

# 请一同阅读原插件文件自带的readme

# 安装求生之前的命令
```shell
dpkg --add-architecture i386 # anable multi-arch
apt-get update && apt-get upgrade
apt-get install libc6:i386 # install base 32bit libraries
apt-get install lib32z1
apt-get install screen
```

# 新建一个用户来代替root执行操作
新建steam用户并把它添加到sudo的用户组里面，然后登陆
```shell
adduser steam
adduser steam sudo
login
```

# 安装steam以及软件的过程
安装steam的linux客户端以及求生服务器软件的相关命令。
```shell
wget http://media.steampowered.com/installer/steamcmd_linux.tar.gz
tar -xvzf steamcmd_linux.tar.gz
./steamcmd.sh
force_install_dir ./Steam/steamapps/common/l4d2
login anonymous
app_update 222860 validate
quit
```

# 通过指令下载zonemod
在github上下载解压覆盖安装后，然后移除压缩包
```sh
cd ~/Steam/steamapps/common/l4d2/left4dead2
wget https://github.com/SirPlease/L4D2-Competitive-Rework/archive/refs/heads/master.zip
unzip master && rm master.zip
cp -r L4D2-Competitive-Rework-master/* ~/Steam/steamapps/common/l4d2/left4dead2
rm -rf L4D2-Competitive-Rework-master
```

# 管理员设置的路径
`/home/steam/Steam/steamapps/common/l4d2/left4dead2/addons/sourcemod/configs`

# ast笔记
接下来就是安装ast了。

核心还是看看他是怎么在ZoneMod的基础上安装。

他的安装首先是在ZoneMod这没得说。
然后他要我备份一个文件，呃，我没想清楚，所以我觉得我是要先看看这个config下的文件是什么东西。

我觉得我最好看一下这个目录里面分别都是些什么东西，心里有个数。

呃，考虑大概的看一下插件里面的内容。也是为了心里有个数。

ast这里我没仔细看，我直接覆盖安装了。
我只备份了原本的ZoneMod文件夹。
还有特别关注了一下admin开头的文件。

ast基于zonemod，在用他的文件覆盖完之后，你还需要用特定的启动文件启动才能达到你想要的那个效果。

那我有一个问题，难道他没办法和ZoneMod的共存吗？

之后再考虑吧，现在不想这个。

==还有一个，我个人觉得这个可以直接用，不用事先安装zonemod，因为它没有必要那么弄嘛==
提到zonemod的目的只是为了安装罢了。

似乎是不能直接用的。

```sh
# 这部分是进行备份
cp -r ~/Steam/steamapps/common/l4d2/left4dead2/addons/ /home/lighthouse/l4d 这里把路径补充完整就行

cp -r /home/lighthouse/Steam/steamapps/common/l4d2/left4dead2/cfg/ 一样

cp -r /home/lighthouse/Steam/steamapps/common/l4d2/left4dead2/scripts/ 一样
# 这部分是恢复备份，是需要写到脚本文件里面的
cp -r ~/l4d2_plugins_files/pure/l4d2/* ~/Steam/steamapps/common/l4d2/
```

上面三个复制进left4dead2，这样就直接复制了。

另外可以添加motd文件和另外的一个与服务器内容有关的东西。

至于zonemod和ast的话应该是直接覆盖根目录比较好
zonemod的：
```sh
# zonemod安装
cp -r ~/l4d2_plugins_files/zonemod/L4D2-Competitive-Rework-master/* ~/Steam/steamapps/common/l4d2/left4dead2

# ast覆盖zonemod来安装
cp -r ~/l4d2_plugins_files/astmod/AST/* ~/Steam/steamapps/common/l4d2/
```




# 切换插件的脚本
```sh
echo 如果要切换插件的话，先恢复原版，然后执行对应的操作；
# 恢复原版
rm -rf ~/Steam/steamapps/common/l4d2/left4dead2/addons
rm -rf ~/Steam/steamapps/common/l4d2/left4dead2/cfg
rm -rf ~/Steam/steamapps/common/l4d2/left4dead2/scripts

cp 
```
