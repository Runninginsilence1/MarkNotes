# Linux考试内容

Linux一共六个相关实验

考试则是设置具体案例，来从中考试相关的知识点...



首先是在CentOS上的软件包管理工具的使用，这是基础。



## RPM

shell命令：

**安装**

rpm 	[-i 选项] 	软件包名称

-v	显示安装过程

-h	显示安装进度

--replacepkgs	重复安装



**查询**

rpm 	[-q 选项] 	软件包名称

-a   查询已安装的所有软件包

配合管道和grep指令使用可以**查看指定软件是否安装**



## YUM

yum是一个基于rpm技术的实用软件管理技术。比rpm更方便

yum的优势在于它能根据软件包头部信息自动分析相关依赖以及拥有相关的远程仓库。



配置文件位置在 /etc/yum.conf



使用yum指令来安装软件：

yum 	 install	软件包名





## Linux的网络基础

网络配置的相关名词

**主机名**

略

**IP地址**

略

**子网掩码**

有时候会将网络分为多个子网络。子网掩码是用来区分它们的。

**网关地址**

配置好IP地址和子网掩码后，主机可以**和同一个网段的其他主机进行通信**。

设置网关可以实现**不同网段之间的通信**。

**DNS服务器**

DNS本质是IP地址的解析服务。

直接使用IP地址也可以访问主机，就是太难记。

DNS服务器的运行大概是主机向指定的DNS服务器查询对应的映射，然后进行连接。

### 网络配置文件

/etc/sysconfig/network-scripts/ifcfg-eth0

网卡的相关配置在这个文件中



### 网络配置的相关shell命令

- hostname 查看主机名

- ifconfig 	[网络接口名]	[ip地址]	[netmask 子网掩码]	[up | down]

重要命令，可以查看网络接口的配置情况，并**临时**设置网卡、激活和停用网络端口。

想要永久修改必须修改相关的配置的配置文件并重启

​	例如：将网卡的IP地址临时设置成192.168.0.10

​	ifconfig eth0 1192.168.0.10



- ping [ip地址] 	测试网络的连接情况。



### linux的网络服务

linux的网络由这些软件来进行管理

- web	Apache

- DHCP	Dhcp 

- FTP	Vsftpd

- DNS 	bind

- Samba	Samba



他们的服务通常由**守护进程**控制。



管理服务的相关shell命令

service	服务名	start|stop|restart





## DNS服务器

因为只能用学校的电脑来考，所以限定CentOS的版本为6.5

使用软件Bind的守护进程named来完成域名解析服务。

### DNS服务器的安装与准备

Bind相关的软件包有两个：

bind和bind-chroot，后者可以提高DNS服务的安全性（把程序的文件操作权限限定在唯一的一个文件夹内）

使用yum进行安装。

如果本地有安装包也可以用rpm命令安装。

需要允许该服务**能通过防火墙**。或者使用service iptables stop 暂时关闭防火墙



### DNS服务器的配置

/etc/named.conf	设置DNS服务器的全局参数，是最重要的配置文件。

/etc/named.rfc1912.zones	声明域(domain)的文件

/var/named/named.ca	缓存服务器的配置文件，通常不需要修改

正向和反向区域文件	有named.conf指定，实现主机名和IP的相互转换





