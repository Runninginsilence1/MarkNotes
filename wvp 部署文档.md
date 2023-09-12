
不是根据官方文档来的。
使用了别人提供好的版本以及 编译好内容；


# 必要环境

必备 mysql和redis用于给 wvp使用；
必备openssl，但是不知道是给 wvp还是zlm使用的；


把所有的文件全部弄到 `/home/zenglg`目录下，在root权限下。

jdk： 使用 install jdk脚本直接安装； 使用 java 命令测试是否安装成功
redis： 使用 install redis脚本直接安装； 使用 `systemctl status redis` 查看服务是否启动。
mysql： 使用 1.sh 脚本安装。 安装后的mysql关闭了权限管理，mysql在初次安装时会随机生成一个root账户的密码，脚本里面获得并存到 InitPasswd（大概是这个名字）变量里面了。 先去 `/etc/my.cnf` 中关闭 skip 那个选项（注释掉），然后进入到 mysql 里面，使用命令改掉root的登录密码： `ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'Zenglg2012!@#';` 这个命令似乎只能在mysql 5.7的版本使用，8.0以上的版本文件改掉了似乎是。  然后为了你的电脑可以访问到远程的mysql服务器， 需要修改登录权限： `GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY'Zenglg2012!@#' WITH GRANT OPTION;` 意思是任何ip都可以通过 root账户以及密码来访问mysql实例中的任意数据库的任意表。 完成后使用 `systemctl status mysql` 来查看服务是否启动，并用之前设置的密码来查看是否可以访问成功。 然后执行 create_db.sh 脚本初始化数据库数据
openssl： 直接使用脚本来安装。 直接使用openssl 命令来查看是否安装成功。
zlm： 根据service的启动脚本来直接启动。 直接使用它的默认配置。 直接启动一次看有没有致命错误。
wvp： 修改 wvp_config.sh这个脚本，然后运行。 确保配置文件没有问题。  尝试启动，zlm； mysql； 和redis应该都是没有问题的。

