# t2绕过部署文档

# 郑子康补充：通过chroot隔离启动chromium

## 安装环境流程
所有命令使用root账户执行
四个步骤：
1. 在外部安装debootstrap，它可以让chroot里面拥有基本的linux环境；然后将需要必须的文件通过mount（或者cp），使隔离环境可以访问到；
2. 使用chroot进入隔离环境之后，执行启动chrome所必须要的步骤；
3. 退出隔离环境，然后调用管理环境里面的脚本启动chrome；
4. 隔离环境里面要有相关的脚本，他负责在隔离环境中启动

## 脚本
启动脚本为run.sh，它会调用其他的脚本，所以需要确保以下脚本在同级目录，root用户执行
如果是第一次安装，以下脚本会按顺序执行：
init_chroot.sh  在外部初始化chroot目录
setup_chroot.sh 在chroot内部安装chrome
run_chroot.sh 启动chroot环境的脚本
run_in_chroot.sh 在chroot环境内部负责启动chrome的脚本

run.sh
```sh
# 初始化过chroot环境了吗？
target_directory="/chroot"
if [ ! -d "$target_directory" ]; then
    echo "目录尚未初始化，开始初始化"
    ./init_chroot.sh
fi

# 启动chroot环境，并指定启动命令
./run_chroot.sh
```


init_chroot.sh
```sh
mkdir /chroot
sudo apt update
sudo apt install -y debootstrap
sudo debootstrap --arch amd64 buster /chroot  https://mirrors.163.com/ubuntu/
mkdir -p /chroot/tmp/.X11-unix

# 将原系统的目录挂载到chroot中
sudo mount --bind /sys /chroot/sys
sudo mount --bind /dev /chroot/dev
sudo mount --bind /proc /chroot/proc
mount --bind /tmp/.X11-unix/ /chroot/tmp/.X11-unix/

# 允许访问图形化终端，启动chrome要用到
xhost + local:

# 进入chroot环境，并启动内部的脚本
cp ./setup_chroot.sh /chroot/setup_chroot.sh
sudo chroot /chroot ./setup_chroot.sh

# chroot环境初始化完成
echo "chroot环境初始化完成"

```

setup_chroot.sh
```sh
apt update
# 安装chrome
apt install -y chromium
# 安装chrome需要的中文字体
apt-get install -y ttf-wqy-microhei ttf-wqy-zenhei xfonts-wqy

export DISPLAY=:0
# 如果没有问题的话就正常退出
exit
```

run_chroot.sh
```sh
# 先取消挂载
sudo umount /chroot/sys
sudo umount /chroot/dev
sudo umount /chroot/proc
sudo umount /chroot/tmp/.X11-unix/

# 重新挂载
sudo mount --bind /sys /chroot/sys
sudo mount --bind /dev /chroot/dev
sudo mount --bind /proc /chroot/proc
sudo mount --bind /tmp/.X11-unix/ /chroot/tmp/.X11-unix/

xhost + local:

# 进入chroot
cp -f run_in_chroot.sh /chroot/run_in_chroot.sh
sudo chroot /chroot ./run_in_chroot.sh
```

run_in_chroot.sh
```sh
# 环境变量，以后考虑加入
export DISPLAY=:0
# 先用百度测试
chromium  --ignore-certificate-errors --kiosk --no-first-run --noerrdialogs --disable-infobars --disable-session-crashed-bubble --incognito "https://www.baidu.com/" --no-sandbox
```

# 必备环境
目前必须是`ubuntu22.04`

# 下载相关依赖

```shell
apt update
apt-get install libxml2-dev python-pip -y
#下载openbox，这个应该就是实现全屏化的软件
apt install openbox -y
#安装依赖
apt install xinit -y
sudo apt-get install chromium-browser -y
```

## 安装Nodm

**配置系统自动登录**：要实现无人值守的Kiosk模式，需要配置系统在启动时自动登录。这样可以确保在系统启动时，Kiosk模式会自动运行。对于基于Debian或Ubuntu的系统，可以使用以下命令安装`nodm`来实现自动登录：

```
sudo apt-get install nodm -y
```

接下来，编辑`/etc/default/nodm`文件并设置以下内容（将`your_username`替换为实际的用户名）：

```
NODM_ENABLED=true
NODM_USER=test
```

. **配置启动Kiosk模式**：为了在用户登录时自动启动Kiosk模式，需要将启动脚本添加到用户的Xsession配置中。将以下内容添加到`/home/test/.xsession`文件中（如果文件不存在，请创建一个新文件）：

```
/home/demo/start.sh
```

 **start.sh脚本内容**

```shell
#!/bin/bash
cd /home/demo && sudo  nohup ./moss &
openbox-session &
# 启动Chromium浏览器（请根据需要更改URL）
chromium-browser  --ignore-certificate-errors --kiosk --no-first-run --noerrdialogs --disable-infobars --disable-session-crashed-bubble --incognito "http://127.0.0.1:9999/"

```

 --ignore-certificate-errors

# 设置用户登录root无需输入密码

```shell
sudo visudo


#在最后一行添加
test  ALL=(ALL:ALL) NOPASSWD: ALL
```



## 内核参数来关闭系统启动时的输出

1. 打开GRUB配置文件：使用文本编辑器打开`/etc/default/grub`文件。需要使用root权限打开，例如在Ubuntu系统中，可以使用以下命令：

   ```
   sudo nano /etc/default/grub
   ```

2. 修改`GRUB_CMDLINE_LINUX_DEFAULT`参数：在`/etc/default/grub`文件中，找到`GRUB_CMDLINE_LINUX_DEFAULT`这一行，将`quiet`参数添加到引号内。`quiet`参数会让系统启动时尽可能少地显示输出信息。另外，你还可以添加`loglevel=0`参数，这会进一步减少系统启动时的输出信息。修改后的参数看起来像这样：

   ```
   GRUB_CMDLINE_LINUX_DEFAULT="quiet loglevel=0"
   ```

3. 更新GRUB：保存并关闭`/etc/default/grub`文件。然后，在终端中运行以下命令，以使更改生效：

   ```
   sudo update-grub
   ```

   对于某些发行版，你可能需要使用`sudo grub2-mkconfig -o /boot/grub2/grub.cfg`命令更新GRUB。

4. 重启系统：现在，你可以重启系统以查看更改是否生效。系统启动时的输出信息应该大大减少。

这些步骤适用于使用GRUB引导加载器的Linux发行版。如果你使用的是其他引导加载器（如systemd-boot、LILO等），请参阅相应的文档以获取如何修改内核参数的详细信息。



sudo copy -r /run/* /home/ubuntu/chroot_env/run

