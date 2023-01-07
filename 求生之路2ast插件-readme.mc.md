# ZoneMod 及 AST插件安装指南
首先需要购买云服务器，因为ZoneMod只能在Linux服务器上运行，所以需要购买Linux发行版的镜像，一般是Ubuntu或者CentOS。

# 视频的索引
00:14 ~ 04:40 云服务器购买以及远程SSH前的配置
04:46 ~ 10:09 通过XShell登录远程终端以及环境搭建
10:21 ~ 12:45 安装steamcmd以及l4d2软件
13:38 ~ 14:57 配置可以一键启动/重启/关闭 服务器的文件
15:09 ~ 17:18 下载ZoneMod相关的文件并通过覆盖文件的方式来安装ZoneMod插件
17:29 ~ 18:40 设置server.cfg来设置启动的配置
19:01 ~ 21:41 让启动文件生效，放行端口，复制ip进入游戏测试，大功告成！


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

# 

