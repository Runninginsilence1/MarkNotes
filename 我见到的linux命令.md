groups 可以查看用户组 具体的用法可以参考终端的指南。


`usermod -aG sudo <用户名>` 将用户添加到sudo组。 将用户添加到指定组，这样就拥有了权限。

scp传输文件:
我自己使用成功的案例：
`scp -r C:\zzk\env\video\ test@192.168.3.222:/home/test/zzk/video`


kagami@47.109.128.128:/home/kagami/zenglg
nimamasilajideliyive4000+
-r 递归复制
-P（大写） 指定端口号
-p 保留源文件属性（可能是用户组之类的）


## alias相关的

单用户反正是

ddd 直接打开 .bashrc
zzk标记alias命令的位置

然后常用的命令就写到那里



特定用户写入到 .bashrc中；
全局配置则是 /etc/bashrc中；


alias命令的使用：
直接绑定常用的命令：

如果需要参数的话： 代办


# 运行通过ssh登录到root账户
使用pass 修改了root的密码但是ssh还是无法登入进root：
原因是因为ssh不允许通过root用户进行登录；
```bash
sudo vim /etc/ssh/sshd_config
```

将 PermitRootLogin without-password 注释掉
然后添加 PermitRootLogin yes

然后重启 sshd：

【知识点】配置 linux文件连接

```tex
是的，你可以通过配置SSH密钥来实现免密码SSH登录，这样你就不必每次都输入密码。以下是实现免密码SSH登录的步骤：

1. **生成SSH密钥对：** 在本地计算机上生成SSH密钥对，包括公钥和私钥。你可以使用`ssh-keygen`命令来执行此操作。默认情况下，它会将密钥文件保存在用户的家目录下的`.ssh`目录中。
    
    bashCopy code
    
    `ssh-keygen -t rsa`
    
    这将生成RSA密钥对。你可以按照提示输入密钥保存的位置和密码，或者直接按Enter来使用默认选项。
    
2. **将公钥复制到远程服务器：** 将你的公钥添加到你想要免密码登录的远程服务器上的`~/.ssh/authorized_keys`文件中。你可以使用`ssh-copy-id`命令来简化这个过程。
    
    bashCopy code
    
    `ssh-copy-id username@remote_server_ip`
    
    这将把你的公钥复制到远程服务器上，允许你使用私钥进行身份验证。
    
3. **测试SSH连接：** 现在，你应该能够通过SSH连接到远程服务器而无需输入密码：
    
    bashCopy code
    
    `ssh username@remote_server_ip`
    
    如果一切设置正确，你将可以无密码登录到远程服务器。
    

注意事项：

- 请确保你的私钥文件（通常为`~/.ssh/id_rsa`）在本地计算机上具有适当的权限，通常为`-rw-------`（只有所有者可读可写）。
- 在复制公钥到远程服务器之前，确保远程服务器上的`~/.ssh`目录和`~/.ssh/authorized_keys`文件具有适当的权限。通常，`~/.ssh`目录权限应为`700`，`~/.ssh/authorized_keys`文件权限应为`600`。
- 如果你之前使用密码登录到服务器，现在应该能够使用SSH密钥登录。如果出现问题，你可以尝试使用`ssh -v`选项进行详细的调试。
```

实际操作就是：
`ssh-keygen -t rsa` 在你的操作端生成秘钥；
然后进入~/.ssh中找到pub后缀的公钥，传输到远程服务器中。
然后在远程服务器中 通过 cat 将秘钥添加到 ~/.ssh/aut 什么文件里面。 
大功告成。

# 文本处理三剑客

## grep

标准格式是： grep -i "pattern" file.txt

pattern 默认就是正则表达式。
-i 不区分大小写
-v 反向过滤
-C 3 显示匹配行以及上下文   -A就是上，-B就是下，很好理解。

使用函数我觉得更好应该；


# 环境变量

环境变量是都可以访问到的变量。

使用 export 就可以导出变量。 

然后添加到环境变量的path，里面就可以直接访问到。
格式为： `PATH=/path1:/path2:/path3`

# 位置变量

相当于函数获取参数
一到九 直接为 `$9`， 更多的参数需要是 `${10}`，一般没有这么多参数

`$*` 会把所有参数看成一个整体；
`$@` 同样获取所有的参数，但是不会将他们拼成一个字符串。


# 预定义变量

预先定义好的变量，一般是一些状态、常用的值。

`$$` 当前执行的进程 id
`$!` 最后执行的后台进程的pid
`$?` 上一个命令的执行状态
``

【知识点】使用 netstat 查看网络端口情况和对应的 process

使用命令 `netstat -anp`

【知识点】用户级别切换默认级别的shell

这个就是直接选择用户目录下的 某一个shell，这里用fish举例子
`chsh -s /usr/bin/fish`



【知识点】使得 Screen启动的程序可以查看日志：

screen是支持的，只不过默认没有打开。

gpt说的是有效的。

在你的用户目录下创建 `.screenrc文件`
```sh
termcapinfo xterm* ti@:te@
termcapinfo xterm-color* ti@:te@
defscrollback 10000
```


保存重启 screen 就可以看到效果了。


