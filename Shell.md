# Shell
随便写写

```shell
#!/bin/bash
#######################################参数设置##########################################
NAME="coop"		#screen名称
DIR=/root/Steam/steamapps/common/"Left 4 Dead 2 Dedicated Server"	 #srcds_run位置
STEAMCMD=/root/Steam	#steamCMD位置
PARAMS="-game left4dead2 -condebug -insecure +hostport 27015 +map c2m1_highway +exec server.cfg -tickrate 100 "	#启动参数
WAY=1		#关闭方式选择。1——screen指令关闭；2——screen获取pid后kill；3——ps获取pid后kill

#######################################详细代码##########################################

#启动部分
function StartScreen(){
	StartNameList=`screen -ls | grep ${NAME}`
	if [[ -z $StartNameList ]];then
		cd "$DIR"
		echo -n "[L4D2.sh]开启 ${NAME} 服务器中......"
		screen -dmS ${NAME} ./srcds_run $PARAMS
		sleep 1
		echo "已完成"
	else
		echo "已存在名为${NAME}的screen"
	fi
	return $?
}

#screen检查
function ScreenCheck(){
	CloseNameList=`screen -ls | grep ${NAME}`
	if [[ ! -z $CloseNameList ]];then
		echo -n "检查中..."
		screen -wipe > /dev/null
		screen -ls | grep ${NAME} > /dev/null
		CloseNameList=$?
		[ $CloseNameList -eq 0 ] && echo "关闭异常" || echo "已关闭"
	else
		echo "已关闭"
	fi
}

#关闭部分
function CloseScreen(){
	CloseNameList=`screen -ls | grep ${NAME}`
	if [[ ! -z $CloseNameList ]];then
		echo -n "[L4D2.sh]关闭 ${NAME} 中......"
		if [ $WAY = 1 ];then
			screen -X -S ${NAME} quit
			ScreenCheck
		elif [ $WAY = 2 ];then
			screen -ls |grep ${NAME} |awk -F . '{print $1}'|awk '{print $1}' | xargs kill
			ScreenCheck
		elif [ $WAY = 3 ];then
			ps aux | grep -v grep | grep SCREEN | grep srcds_run | grep ${NAME} | awk '{print $2}' | xargs kill
			ScreenCheck
		else
			echo "未指定关闭方式"
		fi
	else
		echo "未找到名为 ${NAME} 的screen"
	fi
	return $?
}

#更新部分
function Update(){
	cd $STEAMCMD
	./steamcmd.sh +login anonymous +app_Update 222860 validate +quit
	#+force_install_dir ./Steam/steamapps/common/..<Your Path>...
   #此条注释指令的steamcmd指令，可指定安装位置
	return $?
}

#路径检查
function PathCheck(){
	if [ -f "$DIR/srcds_run" ];then
	  echo "[L4D2.sh]srcds_run路径正常"
	else
	  echo "[L4D2.sh]srcds_run路径异常"
	fi
	if [ -f "$STEAMCMD/steamcmd.sh" ];then
	  echo "[L4D2.sh]steamCMD路径正常"
	else
	  echo "[L4D2.sh]steamCMD路径异常"
	fi
	return $?
}

#交互部分
function MainBody(){
	echo "####[ L4D2.sh ]#####"
	echo "1——开启服务器"
	echo "2——关闭服务器"
	echo "3——重启服务器"
	echo "4——查看服务器"
	echo "5——更新游戏并重启"
	echo "6——路径检查"
	echo "##################"
	read -n 1 -p "请输入对应数字选择功能:" answer
	echo
	case $answer in
	1 | s)
		StartScreen;;
	2 | c)
		CloseScreen;;
	3 | r)
		echo "[L4D2.sh]执行重启步骤"
		CloseScreen
		StartScreen;;
	4 | l)
		screen -ls
		echo "以下为详细参数："
		ps aux | grep -v grep | awk '{print $1,$2,$11,$12,$13,$14,$15,$16,$17,$18,$19,$20,$21,$22,$23,$24,$25,$26,$27,$28,$29,$30,$31,$32,$33,$34,$35,$36,$37,$38}'| grep SCREEN --color
		;;
	5 | u)
		echo "[L4D2.sh]执行更新步骤"
		CloseScreen
		Update
		StartScreen;;
	6 | p)
		PathCheck;;
	*)
		echo "[L4D2.sh]未知指令，请重试";;
	esac
	return $?
}

MainBody
```
