# l4d2_dockerfile
以下是构建l4d2_anne镜像的dockerfile文件。

```dockerfile
# 基于debian镜像构建，ubuntu就是其中一种
FROM debian:buster-slim

# 为包管理器进行配置：添加对应cpu架构的声明，更行apt
RUN dpkg --add-architecture i386 && apt-get update
# 安装：
# curl：经典的命令行工具，用来请求web服务器
# iputils-ping：经典的ping软件，用来测试主机是否可达；
# wget：经典的命令行工具，用来从指定url上下载文件；
# file：不知道什么玩意，不过确实是rpm上面的一个包
# tar：linux上常用的压缩软件，在软件的安装上用的比较多
# bzip2：同上，也是压缩软件
# locales：linux上的多语言环境，非重点
# gzip, unzip：用于zip算法的解压缩
# bsdmainutils：重要的工具，不知道别的
# ...暂时就写这么多，
RUN apt-get install -y curl iputils-ping wget file tar bzip2 locales gzip unzip bsdmainutils python3 lib32z1 util-linux ca-certificates binutils bc jq tmux netcat lib32gcc1 lib32stdc++6 git nano
# 我查了一下
# sed命令的全称是stream Editor，也就是说这一段命令适用于编辑什么东西的。
# 从这里的locale和utf-8来看，应该是修改区域，总之是做一些初始化的配置
RUN sed -i -e 's/# en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/' /etc/locale.gen && \
    dpkg-reconfigure --frontend=noninteractive locales && \
    update-locale LANG=en_US.UTF-8

# 这里注释掉了，那就不看
#RUN echo "\n* soft core 0\n* hard core 0" >> /etc/security/limits.conf
#RUN echo "fs.suid_dumpable=0" >> /etc/sysctl.conf

# ENV用于在dockerfile定义环境变量
# 似乎和系统默认的export指令有着密切的关系
# 需要一部分前置知识，学习起来有成本，那就暂时不管
ENV LANG en_US.UTF-8 

# 添加一个名字叫louis的用户，-m表示一并添加用户目录 /home/louis
RUN useradd -m louis
# 字面意思，就是设置工作目录的意思
WORKDIR /home/louis
# 选择linux用户为louis
USER louis

# 安装steam的cmd版
# 通过wget命令下载资源，然后解压压缩包，板厚移除压缩包，+quit应该是附加退出命令，执行在steamcmd客户端里面的。
RUN wget http://media.steampowered.com/installer/steamcmd_linux.tar.gz && tar -xzf steamcmd_linux.tar.gz \
    && rm steamcmd_linux.tar.gz && ./steamcmd.sh +quit
# 创建文件夹： .steam/sdk32/，-p的意思是没有父目录就一并创建父目录
# ln命令用于创建链接，这里是创建的符号链接，软连接，相当于快捷方式，前面是连接名字，后面是实际文件的路径
# 这里应该是不同位数系统的so文件分别创建了符号链接
RUN mkdir -p .steam/sdk32/ && ln -s ~/linux32/steamclient.so ~/.steam/sdk32/steamclient.so \
    && mkdir -p .steam/sdk64/ && ln -s ~/linux64/steamclient.so ~/.steam/sdk64/steamclient.so
# 登录steam客户端，匿名登录（指令中可以用空格，知道下一个加号为止），设置目录，下载，然后退出
RUN ./steamcmd.sh +login anonymous +force_install_dir ./l4d2 +app_update 222860 +quit
# 这里粗略的看了一下，--depth选项指定克隆的深度，比如只克隆最近的一次commit，而不是整个项目，相当于只下载最新的版本；
# -b选项可以在克隆时顺便创建一个有别main的分支（我猜的，没实际测试过），命令为zonemod
RUN git clone --depth 1 -b zonemod https://github.com/fantasylidong/anne.git
# 移除anne中的脚本文件夹，这涉及到插件的问题，暂时不管
RUN rm -rf anne/left4dead2/addons/sourcemod/scripting/
# 这个是第二个插件吧，看名字似乎是纯净战役
RUN git clone --depth 1 https://github.com/fantasylidong/purecoop.git
# neko，不过这里怎么是命名的mysql呢？而且他可以直接从github进行clone，没提到它是怎么弄的代理之类的东西啊...
RUN git clone --depth 1 -b mysql https://github.com/fantasylidong/neko.git
# 这里似乎是把完整的插件，整个仓库的内容都弄进来了。
RUN git clone https://github.com/fantasylidong/CompetitiveWithAnne.git
# 翻译：100tick刷新率纯净对抗
RUN git clone --depth 1 https://github.com/fantasylidong/100tickPureVersus.git

# 上面这些仓库以后可以去看看
EXPOSE 27015/tcp
EXPOSE 27015/udp

# 设置环境变量
# 应该就是定义一些常量，然后整个容器内，bash内都通用？
ENV PORT=2333 \
    PLAYERS=10 \
    MAP="c2m1_highway" \
    REGION=255 \
    HOSTNAME="leo fighting" \
    plugin="null" \
	steamid="STEAM_1:1:121430603" \
	password="123456" \
	steamgroup="123456" \
	stripper="false" \
	steam64="" \
	mysql="" \
	mysqlport="" \
	mysqluser="" \
	mysqlpassword="" \
	dlurl=""
# ADD命令似乎类似于CP，不过可以自动解压缩，这里应该就是直接把外部的这个文件复制到容器内部去
ADD entrypoint.sh entrypoint.sh
# 推测这个就是容器启动后的入口脚本了，也就是所谓的入口？
ENTRYPOINT ["sh", "entrypoint.sh"]
```

随后就是这个脚本：
一起写到里面去，笔记更新到linux教程里面去；
那同样的，这个我也懒得看了，学习起来有成本，而我也没有

```sh
#!/bin/bash
# Update Game
# 更新游戏
./steamcmd.sh +login anonymous +force_install_dir ./l4d2 +app_update 222860 +quit

#Softlink l4d2 maps to addons folder
#It would more convenience while you want add custom map. Exspecially when you have sourcemod plugins
#you just need mount your extra map folder to docker container /map . 
# 软连接，这样可以用这个map文件夹代替主程序目录内的addons文件夹
# 这使得你在命令行下操作变得额外方便
ln  -s  /map/*  l4d2/left4dead2/addons/
ln  -s  /map2/*  l4d2/left4dead2/addons/
# 函数 oldpluginpackage
oldpluginpackage(){
    # echo命令可以打印string，默认为stdout，然后两个大于号把它重定向到了求生的source插件的，存储管理员steamid的位置，换句话说，这句话是用来添加管理员的。
	echo "\n\"$steamid\" \"99:z\"" >> /home/louis/l4d2/left4dead2/addons/sourcemod/configs/admins_simple.ini
    # 从steamgroup可以判断应该是把服务器与对应的steam组绑定起来
	echo "sv_steamgroup \"$steamgroup\"" >> /home/louis/l4d2/left4dead2/cfg/server.cfg
    # 什么玩意的密码，哎什么玩意他这是
	echo "rcon_password \"$password\"" >> /home/louis/l4d2/left4dead2/cfg/server.cfg
}
# 函数newpluginpackage，全是文本处理，我在考虑要不要仔细地去看这个命令....
newpluginpackage(){
	sed -i "s/13333337/$steamgroup/g"  /home/louis/l4d2/left4dead2/cfg/server.cfg
	sed -i "s/CompetitiveRework/annehappy/g" /home/louis/l4d2/left4dead2/cfg/server.cfg
	sed -i "s/WowYouKnowThePasswordHere/$password/g" /home/louis/l4d2/left4dead2/cfg/server.cfg
}

cloudconfig(){
	#cloud server settings
	#插件处理hidden
	#echo "\nsv_tags hidden" >> /home/louis/l4d2/left4dead2/cfg/server.cfg
	#sed -i "s/nb_update_frequency\ 0.014/nb_update_frequency\ 0.024/" /home/louis/l4d2/left4dead2/cfg/server.cfg
	#sed -i "s/fps_max\ 150/fps_max\ 0/" /home/louis/l4d2/left4dead2/cfg/server.cfg
	sed -i "47 s/\"2\"/\"16\"/" /home/louis/l4d2/left4dead2/addons/sourcemod/configs/sourcebans/sourcebans.cfg
	#cp /home/louis/l4d2/left4dead2/addons/hostname.txt /home/louis/l4d2/left4dead2/addons/sourcemod/configs/hostname/
	cp /home/louis/l4d2/left4dead2/addons/advertisements* /home/louis/l4d2/left4dead2/addons/sourcemod/configs/
	cp /home/louis/l4d2/left4dead2/addons/admins_simple.ini /home/louis/l4d2/left4dead2/addons/sourcemod/configs/
}

localconfig(){
	rm /home/louis/l4d2/left4dead2/addons/sourcemod/plugins/optional/specrates.smx
	sed -i "s/fps_max\ 0/fps_max\ 150/" /home/louis/l4d2/left4dead2/cfg/server.cfg
	if [ "$PORT" = "2330" ];
	then
		#git -C /home/louis/CompetitiveWithAnne/ checkout test
		cp  -r /home/louis/CompetitiveWithAnne/* l4d2/left4dead2/
		rm /home/louis/l4d2/left4dead2/addons/sourcemod/plugins/optional/AnneHappy/sam_vs.smx
		sed -i "256 s/-secure/-insecure/"  /home/louis/entrypoint.sh
		sed -i "s/join_autoupdate\ 1/join_autoupdate\ 0/g" /home/louis/l4d2/left4dead2/cfg/cfgogl/*/shared_settings.cfg
		sed -i "s/join_autoupdate\ 4/join_autoupdate\ 0/g" /home/louis/l4d2/left4dead2/cfg/cfgogl/*/shared_settings.cfg
		echo "sm_cvar join_autoupdate 0" >> /home/louis/l4d2/left4dead2/cfg/server.cfg
	fi
	if [ "$PORT" = "2340" ];
	then
		sed -i 's/AnneHappy6.cfg/AnneHappy4.cfg/g' /home/louis/l4d2/left4dead2/cfg/cfgogl/annehappy/confogl_plugins.cfg
		sed -i 's/ai_Tank_StopDistance\ 135/ai_Tank_StopDistance\ 145/' /home/louis/l4d2/left4dead2/cfg/vote/hard_off.cfg
		sed -i 's/ai_TankAirAngleRestrict\ 57/ai_TankAirAngleRestrict\ 60/' /home/louis/l4d2/left4dead2/cfg/vote/hard_off.cfg
	fi
	if [ "$PORT" = "2341" ];
	then
		sed -i 's/AnneHappy6.cfg/AnneHappy5.cfg/g' /home/louis/l4d2/left4dead2/cfg/cfgogl/annehappy/confogl_plugins.cfg
		sed -i 's/ai_Tank_StopDistance\ 135/ai_Tank_StopDistance\ 140/' /home/louis/l4d2/left4dead2/cfg/vote/hard_off.cfg
		sed -i 's/ai_TankAirAngleRestrict\ 57/ai_TankAirAngleRestrict\ 60/' /home/louis/l4d2/left4dead2/cfg/vote/hard_off.cfg
	fi
	if [ "$PORT" = "2342" ];
	then
		sed -i 's/ai_Tank_StopDistance\ 135/ai_Tank_StopDistance\ 135/' /home/louis/l4d2/left4dead2/cfg/vote/hard_off.cfg
		sed -i 's/ai_TankAirAngleRestrict\ 57/ai_TankAirAngleRestrict\ 60/' /home/louis/l4d2/left4dead2/cfg/vote/hard_off.cfg
	fi
}
copydanceresource(){
	cp -r /home/louis/anne/left4dead2/sound/ l4d2/left4dead2/
	cp -r /home/louis/anne/left4dead2/models/ l4d2/left4dead2/
}
anneremovemysql(){
	#修改hostname插件端口对应port环境变量
	if [ -n "$PORT" ]
	then
		sed -i "s/2330/$PORT/g" /home/louis/l4d2/left4dead2/addons/sourcemod/configs/hostname/hostname.txt
	fi
	
	if [ -n "$hostname" ]
	then
		sed -i "s/Anne电信测试服/$hostname/g" /home/louis/l4d2/left4dead2/addons/sourcemod/configs/hostname/hostname.txt
	fi
	
	if [ -n "$mysqlexist" ]
	then
		rm /home/louis/l4d2/left4dead2/addons/sourcemod/plugins/extend/l4d_stats.smx
                rm /home/louis/l4d2/left4dead2/addons/sourcemod/plugins/chat-processor.smx
                rm /home/louis/l4d2/left4dead2/addons/sourcemod/plugins/extend/hextags.smx
                rm /home/louis/l4d2/left4dead2/addons/sourcemod/plugins/extend/lilac.smx
                rm /home/louis/l4d2/left4dead2/addons/sourcemod/plugins/extend/sbpp_*
                rm /home/louis/l4d2/left4dead2/addons/sourcemod/plugins/extend/rpg.smx
                rm /home/louis/l4d2/left4dead2/addons/sourcemod/plugins/extend/chatlog.smx
                rm /home/louis/l4d2/left4dead2/addons/sourcemod/plugins/extend/veterans.smx
                cp /home/louis/l4d2/left4dead2/addons/sourcemod/plugins/disabled/rpg.smx /home/louis/l4d2/left4dead2/addons/sourcemod/plugins/extend/
	fi
}
# plugins Config
if [ ! -d "/home/louis/l4d2/left4dead2/addons/sourcemod/" ];
then
	if [ "$plugin" = "anne" ];
	then
		cp  -r /home/louis/anne/* l4d2/
		if [ "$cloud" = "true" ];
		then
			cloudconfig
		else
			localconfig
		fi
		oldpluginpackage
		echo "anne plugins packge installed"
	fi

	if [ "$plugin" = "purecoop" ];
	then
		cp  -r /home/louis/purecoop/* l4d2/left4dead2/
		echo "purecoop plugins packge installed"
		oldpluginpackage
	fi


	if [ "$plugin" = "neko" ];
	then
		cp  -r /home/louis/neko/* l4d2/left4dead2/
		copydanceresource
		echo "neko plugins packge installed"
		oldpluginpackage
	fi
	
	if [ "$plugin" = "zone" ];
	then
		cp  -r /home/louis/CompetitiveWithAnne/* l4d2/left4dead2/
		# nav file copy
		cp -r /home/louis/anne/update/* l4d2/update/
		copydanceresource
		echo "zone plugins packge installed"
		sed -i "s/join_autoupdate\ 1/join_autoupdate\ 4/g" /home/louis/l4d2/left4dead2/cfg/cfgogl/*/shared_settings.cfg
		if [ "$cloud" = "true" ];
		then
			cloudconfig
		else
			localconfig
		fi
		anneremovemysql
		newpluginpackage
		if [ -n "$PLAYERS" ]
		then
			sed -i "s/mv_maxplayers\ 8/mv_maxplayers\ $PLAYERS/g" /home/louis/l4d2/left4dead2/cfg/server.cfg
		fi
	fi
	
	if [ "$plugin" = "pureversus" ];
	then
		cp  -r /home/louis/100tickPureVersus/* l4d2/left4dead2/
		copydanceresource

                #if [ "$PORT" = "2341" ];
		#then
		#	sed -i 's/mutation12/versus/g' /home/louis/l4d2/left4dead2/cfg/server.cfg
		#fi

                #if [ "$PORT" = "2342" ];
		#then
		#	sed -i 's/mutation12/versus/g' /home/louis/l4d2/left4dead2/cfg/server.cfg
		#fi
		
		if [ -n "$PORT" ]
		then
			sed -i "s/2351/$PORT/g" /home/louis/l4d2/left4dead2/addons/sourcemod/data/hostname.txt
		fi

		if [ -n "$hostname" ]
		then
			sed -i "s/电信服进阶写实包抗/$hostname/g" /home/louis/l4d2/left4dead2/addons/sourcemod/data/hostname.txt
		fi
		
		echo "pure versus packge installed"
		oldpluginpackage
	fi
	
	#if [ "$cloud" = "false" ];
	#then
	#	#localserver change to static local ip
	#	sed -i 's/home.trygek.com/10.0.0.4/g' /home/louis/l4d2/left4dead2/addons/sourcemod/configs/databases.cfg
	#	sed -i 's/12345/3306/g' /home/louis/l4d2/left4dead2/addons/sourcemod/configs/databases.cfg
	#fi
	
	#region setting
	echo "\nsv_region \"$REGION\"" >> /home/louis/l4d2/left4dead2/cfg/server.cfg
	if [ -n "$steamid" ]
	then
		echo "\n\"$steamid\" \"99:z\"" >> /home/louis/l4d2/left4dead2/addons/sourcemod/configs/admins_simple.ini
	fi
	
	#server language setting
	if [ -n "$lang" ]
	then
		sed -i "s/\"ServerLang\"\	\"en\"/\"ServerLang\"\	\"$lang\"/g" /home/louis/l4d2/left4dead2/addons/sourcemod/configs/core.cfg
	else
		sed -i "s/\"ServerLang\"\	\"en\"/\"ServerLang\"\	\"chi\"/g" /home/louis/l4d2/left4dead2/addons/sourcemod/configs/core.cfg
	fi
	
	#修改accelator log report
	if [ -n "$steam64" ]
	then
		sed -i "s/1111111111/$steam64/g" /home/louis/l4d2/left4dead2/addons/sourcemod/configs/core.cfg
	fi
	
	#修改updaterfrequence
	if [ -n "$freqency" ]
	then
		sed -i "s/nb_update_frequency\ 0.024/nb_update_frequency\ 0.0$freqency/" /home/louis/l4d2/left4dead2/cfg/server.cfg
	fi
	
	#修改数据库地址
	if [ -n "$mysql" ]
	then
		sed -i "s/home.trygek.com/$mysql/g" /home/louis/l4d2/left4dead2/addons/sourcemod/configs/databases.cfg
	fi
	
	#修改数据库端口
	if [ -n "$mysqlport" ]
	then
		sed -i "s/12345/$mysqlport/g" /home/louis/l4d2/left4dead2/addons/sourcemod/configs/databases.cfg
	fi
	
	#修改数据库用户名
	if [ -n "$mysqluser" ]
	then
		sed -i "s/morzlee/$mysqluser/g" /home/louis/l4d2/left4dead2/addons/sourcemod/configs/databases.cfg
	fi
	
	#修改数据库密码
	if [ -n "$mysqlpassword" ]
	then
		sed -i "s/anne123/$mysqlpassword/g" /home/louis/l4d2/left4dead2/addons/sourcemod/configs/databases.cfg
	fi
	
	#修改模型下载链接
	if [ -n "$mysqlpassword" ]
	then
		sed -i "s/sb.trygek.com/$dlurl/g" /home/louis/l4d2/left4dead2/cfg/server.cfg
	fi
	
	#private,把服务器设为只允许组员第一个进入
	if [ -n "$private" ]
	then	
		if [ "$plugin" = "zone" ];
		then
			sed -i "s/sm_cvar\ sv_steamgroup_exclusive/\/\/sm_cvar\ sv_steamgroup_exclusive/g" /home/louis/l4d2/left4dead2/cfg/server.cfg
			sed -i "s/sv_steamgroup_exclusive/\/\/sv_steamgroup_exclusive/g" /home/louis/l4d2/left4dead2/cfg/server.cfg
			echo "sm_cvar join_enable_autolobbycontrol 1" >> /home/louis/l4d2/left4dead2/cfg/server.cfg
		else
			sed -i "s/sm_cvar\ sv_steamgroup_exclusive\ 0/sm_cvar\ sv_steamgroup_exclusive\ 1/g" /home/louis/l4d2/left4dead2/cfg/server.cfg
			sed -i "s/sv_steamgroup_exclusive\ 0/sv_steamgroup_exclusive\ 1/g" /home/louis/l4d2/left4dead2/cfg/server.cfg
		fi
	fi
	
	if [ "$lobby" = "true" ];
	then
		sed -i "s/sv_allow_lobby_connect_only/\/\/sv_allow_lobby_connect_only/g" /home/louis/l4d2/left4dead2/cfg/server.cfg
		echo "sm_cvar sv_hosting_lobby 1" >> /home/louis/l4d2/left4dead2/cfg/server.cfg
		if [ "$plugin" = "zone" ];
		then
			sed -i "/sm_killlobbyres/d" /home/louis/l4d2/left4dead2/cfg/cfgogl/*/shared_settings.cfg
			sed -i "/confogl_addcvar\ sv_allow_lobby_connect_only\ 0/d" /home/louis/l4d2/left4dead2/cfg/cfgogl/*/shared_cvars.cfg
			sed -i "s/confogl_match_killlobbyres\ \ \ \ \ \ \ \ \ \ \"1\"/confogl_match_killlobbyres\ \ \ \ \ \ \ \ \ \ \"0\"/g" /home/louis/l4d2/left4dead2/cfg/cfgogl/*/shared_cvars.cfg
		fi
	fi
	
	if [ -n "$hidden" ]
	then	
		echo "\nsv_tags\ hidden" >> /home/louis/l4d2/left4dead2/cfg/server.cfg
	fi
	
	#delete motd
	rm /home/louis/l4d2/left4dead2/*motd.txt
	rm /home/louis/l4d2/left4dead2/*host.txt
fi

# Start Game
cd l4d2 && ./srcds_run -console -game left4dead2 -ip 0.0.0.0 -tickrate 100 -port "$PORT"  +maxplayers "$PLAYERS" +map "$MAP" -secure
```
