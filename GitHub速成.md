# GitHub速成

# Git最重要的是分支的功能，直接跳到那个地方去看：

# GitHub基本概念
重要的一些概念：
Repository：就是仓库。一般一个项目一个仓库。
仓库的意思，即你的项目，你想在 GitHub 上开源一个项目，那就必须要新建一个 Repository
，如果你开源的项目多了，你就拥有了多个 Repositories 。
Issue：意见或建议。给源代码的作者提意见（修改建议或者bug）
问题的意思，举个例子，就是你开源了一个项目，别人发现你的项目中有bug，或者哪些地方
做的不够好，他就可以给你提个 Issue ，即问题，提的问题多了，也就是 Issues ，然后你看
到了这些问题就可以去逐个修复，修复ok了就可以一个个的 Close 掉。
Star：点赞！
这个好理解，就是给项目点赞，但是在 GitHub 上的点赞远比微博、知乎点赞难的多，如果你
有一个项目获得100个star都算很不容易了！
Fork（分支）相当于别人想用你的开源代码，但是你又不想别人影响你的代码，所以别人相当于复制了一份你的代码到他那里去了。
这个不好翻译，如果实在要翻译我把他翻译成分叉，什么意思呢？你开源了一个项目，别人
想在你这个项目的基础上做些改进，然后应用到自己的项目中，这个时候他就可以 Fork 你的
项目，这个时候他的 GitHub 主页上就多了一个项目，只不过这个项目是基于你的项目基础
（本质上是在原有项目的基础上新建了一个分支，分支的概念后面会在讲解Git的时候说
到），他就可以随心所欲的去改进，但是丝毫不会影响原有项目的代码与结构。
Pull Request：别人把你的代码改的很不错，你觉得可以直接合并进你的代码了，就发起这个请求。
发起请求，这个其实是基于 Fork 的，还是上面那个例子，如果别人在你基础上做了改进，后
来觉得改进的很不错，应该要把这些改进让更多的人收益，于是就想把自己的改进合并到原
有项目里，这个时候他就可以发起一个 Pull Request（简称PR） ，原有项目创建人就可以收
到这个请求，这个时候他会仔细review你的代码，并且测试觉得OK了，就会接受你的PR，这
个时候你做的改进原有项目就会拥有了。
Watch：相当于Subscribe，关注这个项目的第一手消息。
这个也好理解就是观察，如果你 Watch 了某个项目，那么以后只要这个项目有任何更新，你
都会第一时间收到关于这个项目的通知提醒。
Gist：小范围分享，仅仅分享一些代码片段。
有些时候你没有项目可以开源，只是单纯的想分享一些代码片段，那这个时候 Gist 就派上用
场了！


# Git的基本命令
git init 初始化当前文件夹为一个git仓库；
git status 查看当前文件夹的git状态
git add 添加文件到暂存区
git rm --cached 可以将暂存区内的缓存移除
git commit 提交！
git log 查看所有的commit记录
git branch 分支命令，这个指令很重要
各自分支的互不影响，最后可以把分支合并到一条主分支上。
执行 git init 初始化git仓库之后会默认生成一个主分支 master ，也是你所在的默认分支，也基
本是实际开发正式环境下的分支，一般情况下 master 分支不会轻易直接在上面操作的，你们
可以输入 git branch 查看下当前分支情况。

git checkout 可以转移分支。
使用-b选项可以实现新建并转移分支，一步到位。

git merge 可以合并指定分支到当前分支

git branch -d 和 -D都是删除，-d是合并完毕后删除，-D是你还没有合并完成，但是你想强制删除。

git tag可以给当前的版本打tag

通过 git checkout 也可以直接切换tag。

# 向 GitHub提交代码！
## SSH
github的验证机制是SSH
> 那么什么是 SSH 呢？ 简单点说，SSH是一种网络协议，用于计算机之间的加密登录。目前
是每一台 Linux 电脑的标准配置。而大多数 Git 服务器都会选择使用 SSH 公钥来进行授权，
所以想要在 GitHub 提交代码的第一步就是要先添加 SSH key 配置。

安装了git bash的话默认就安装了ssh。
可以在git的terminnal里面直接输入ssh查看是否安装。

如果有向git hub上传代码你就要设置ssh密钥，过程如下：
> 输入 ssh-keygen -t rsa ，什么意思呢？就是指定 rsa 算法生成密钥，接着连续三个回
车键（不需要输入密码），然后就会生成两个文件 id_rsa 和 id_rsa.pub ，而 id_rsa 是密钥，
id_rsa.pub 就是公钥。这两文件默认分别在如下目录里生成：

Linux/Mac 系统 在 ~/.ssh 下，win系统在 /c/Documents and Settings/username/.ssh 下，
都是隐藏文件，相信你们有办法查看的。

然后将对应的密钥（id_rsa.pub）添加到 github上，授权成功就可以提交代码了

在 git bash 中输入 ssh -T git@github.com 测试是否添加成功。

## 重要指令push 和 pull
push就是将本地的代码推送到远程服务器中去
`git push origin master`
origin是你要推送的远程仓库的名字，需要你之前使用remote add 定义一个名字。
master是指这里到底是指哪个分支？解决了：
默认是 本地分支名:远程分支名
如果 本地分支和远程分支名称一样就可以省略后面的。

git pull origin
pull就是在你已有仓库的前提下，把远程服务器里面最新的代码拉到本地，并和你的代码进行合并
用法和git push很类似
git clone就是在你本地没有仓库的情况下把远程的仓库直接拉取下来，可以直接开始工作。

## 拉取仓库代码和提交
**没有初始化仓库**的话，（这种情景一般是看别人的开源的项目）就直接使用 git clone指令，后面加 ssh clone的链接；
这样项目到你的设备里的时候就已经是一个 git的项目了，也不需要执行 git init 命令。
使用 git remote -v 查看时，这个项目也只会有一个 远程的仓库，名字就叫 origin。

如果要和现有的项目想push到远程仓库上面去的话，就需要：
1. 在github上建立一个同名的仓库；（不知道需不需要readme.md的文件）

1. 切换项目的根目录，执行这个命令：
`git remote add origin git@github.com:Runninginsilence1/FromZerotoExpert.git`
这句话的意思是**添加一个远程仓库**，remote的意思就是远程的意思，origin就是这个仓库的名字，一般默认这个项目如果**只提交到一个远程仓库**的话就把它命名为 origin。
所以这里为了方便就使用 origin。
后面就是一个ssh的链接。

然后就可以向远程仓库进行提交了。
`git push origin master`

+ [ ] 如何提交到指定的分支呢？todo

查看我们当前项目有哪些远程仓库可以执行如下命令：
`git remote -v`

并且要**记得**设置用户名和邮箱，这些消息会出现在 commit的记录里面；
```shell
git config —global user.name "stormzhang"
git config —global user.email "stormzhang.dev@gmail.com"
```


我昨天在这里中断了，看看我能不能把github的内容全部看完

# Git指令进阶
## 用户名和邮箱
每次 commit会产生一个 log记录，里面包含commit的用户和邮箱，方便其他用户确认。

```shell
git config --global user.name "stormzhang"
git config --global user.email "stormzhang.dev@gmail.com"
```

--global参数的意思设置全局的用户名和邮箱，不加就是**单独给当前的项目设置**

> PS：我们在 GitHub 的每次提交理论上都会在 主页的下面产生一条绿色小方块的记录，如果
你确认你提交了，但是没有绿色方块显示，那肯定是你提交代码配置的邮箱跟你 GitHub 上的
邮箱不一致，GitHub 上的邮箱可以到 Setting -> Emails里查看。


## alias: 设置别名，减少大量的代码量
通过**设置别名可以简化输入。**

例如：给一个固定命令checkout设置别名：
`git config --global alias.co checkout # 别名`

也可以给一个具体的命令设置
```shell
# 给push设置
git config --global alias.psm 'push origin master'
```

格式就是 config 加上 alias点别名，后面加上原指令

总之 git的一些配置都是通过命令`git config`来进行修改的。

git log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --date=relative

配置文件叫 .gitconfig，通过指令`git config -I`查看配置。

## diff指令：查看代码的改动
很有用总之，不过idea图形化可以直接显示，有需要的时候再来学习

## checkout：切换为主，撤销为辅
主要是用来切换**分支和标签(tag)**

撤销的话，是你正在修改的代码你要完全放弃你的修改，那就直接 checkout 当前的文件名

## stash
似乎有点中断那个概念的意思。。。

我有点解释不清，实际上人家也没有解释，那我就直接吧原文贴在这里：

> 设想一个场景，假设我们正在一个新的分支做新的功能，这个时候突然有一个紧急的bug需要
修复，而且修复完之后需要立即发布。当然你说我先把刚写的一点代码进行提交不就行了
么？这样理论上当然是ok的，但是这会产品垃圾commit，原则上我们每次的commit都要有实
际的意义，你的代码只是刚写了一半，还没有什么实际的意义是不建议就这样commit的，那
么有没有一种比较好的办法，可以让我暂时切到别的分支，修复完bug再切回来，而且代码也
能保留的呢？

我理解的：
你正在该bug，而且有一部分你觉得没有问题了，用了git add，然后你继续改；突然来了一个通知，现在有一个重大的bug要修复，你必须先处理这逼玩意先；
然后你又不想放弃你现在的工作进度。。。此时这个 stash就发挥作用了。

这个单词实际就是 藏身处，存放的意思，你可以当做一个和代码无关，**单纯用来保存你宝贵数据的地方**。

现在直接介绍命令：
`git stash`： 把当前分支所有没有 commit 的代码先暂存起来；
`git stash list`：查看通过stash 暂存的代码；
`git stash apply`：恢复代码
`git stash drop`：删除暂存的记录
`git stash pop`：等于 apply + drop，pop就是出栈的意思，应该很好理解；
clear：呃呃

## merge & rebase
两个指令同为合并，只不过一个是暴力合并，后面一个是排序后合并；
根据需求使用。

> rebase 跟 merge 的区别你们可以理解成有两个书架，你需要把两个书架的书整理到一起
去，第一种做法是 merge ，比较粗鲁暴力，就直接腾出一块地方把另一个书架的书全部放进
去，虽然暴力，但是这种做法你可以知道哪些书是来自另一个书架的；第二种做法就是
rebase ，他会把两个书架的书先进行比较，按照购书的时间来给他重新排序，然后重新放置
好，这样做的好处就是合并之后的书架看起来很有逻辑，但是你很难清晰的知道哪些书来自
哪个书架的。

## 解决冲突
现在是我个人...所以这部分内容暂时先不看
而且 有冲突的话就同时去判断代码为什么冲突然后解决。
没有什么傻瓜式的解决方案。


# 分支：最重要的部分
团队合作的核心功能。

## 常用操作
`git branch 分支名`： 建立一个**和当前分支内容完全一样**的新分支
`git checkout 分支名`： 切换到该分支
`git checkout -b 分支名`：合并上面两个操作

`git branch -d (branchname)`：删除指定名称的分支