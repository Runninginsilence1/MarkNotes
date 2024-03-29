# Docker 青空の霞光
来源请求：[Docker 容器技术 已完结](https://www.bilibili.com/video/BV1r34y1p7j9)


# Ubuntu安装Docker
首先是要搭建好软件所需要的环境。
他选用的操作系统是ubuntu的22.04。是腾讯云所能提供的三个从零开始的Ubuntu服务器当中版本最高的一个。  

我的话就先用18那个版本先试一试。  
  
首先去安装一个工具：
```shell
sudo apt-get install ca-certificates curl gnupg lsb-release
```
22.04默认安装。

2023年1月28日 20:15:15 更新
宝塔面板（CentOS Linux release 7.9.2009 (Core)）
默认安装该环境。

然后去安装官方提供GPG签名；  
个人感觉是类似于**安卓包管理的那种签名机制**。照他的来就好了，应该没有问题。
```sh
sudo mkdir -p /etc/apt/keyrings

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

接下来需要把docker的库添加到apt资源列表中。所以他待会儿获取docker软件包的方式应该是通过了apt了。
```sh
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
呃，然后更新一次apt 
```shell
sudo apt update
```

然后安装docker-ce可以了。  
```shell
sudo apt install docker-ce
```

最后将我们当前的用户添加到多个的用户组中，这样的话执行命令就没有那么麻烦了。
```shell
sudo usermod -aG docker <用户名>
```

此时如果通过`docker ps`命令可以出现结果说明 docker已经安装成功。


# 宝塔面板安装docker
腾讯云提供了链接到Docker官方的文档来指导你如何安装Docker。

首先，为了防止残留的数据文件对软件环境产生影响，要求你先把旧版本的docker文件进行移除（即使你从来没有安装过，你也应该知道要这么做。）
```sh
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

执行上面的sh脚本即可。

注意！！如果你以前确实安装过 docker，那么
> Images, containers, volumes, and networks stored in /var/lib/docker/ aren’t automatically removed when you uninstall Docker.

曾经使用过的镜像、容器、数据卷以及网络配置默认都在目录`/var/lib/docker/`目录下，在你通过上面sh脚本卸载docker的时候，这些文件是不会一并移除的。

可以通过多种方式来安装 docker。
> You can set up Docker’s repositories and install from them, for ease of installation and upgrade tasks. This is the recommended approach.

- 可以通过docker的官方仓库来进行docker的安装以及更新。这是推荐的方式。

这里就尝试直接用推荐的这种方式来安装一下docker来试试看。

设置docker的官方仓库。

```sh
sudo yum install -y yum-utils
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```
然后安装docker的引擎：
```sh
sudo yum install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

然后启动docker服务：
```sh
sudo systemctl start docker
```

运行hello程序
```sh
sudo docker run hello-world
```

现在云原生非常流行，至少腾讯云似乎直接提供了docker环境来专门为你运行容器，以后什么时候我可能回去试一试。



# 容器与镜像
# 回顾一下docker的三要素
Docker的三要素是容器，镜像，仓库。

Docker run命令的执行过程：
看了一下他的这个图，我感觉交互的架构和redis是差不多的。
整个核心依旧是server端。

## 镜像
要通过**镜像来才能启动一个容器。**
所以镜像的知识是很重要的。

Docker上保存镜像的官方仓库的网址叫做Dockhub。网址，hub.docker.com
他这里推荐注册个账号，我还没有。因为我可以在终端直接拉取镜像下来，没有要用到账号什么的。

下面是镜像的一些基本操作。
`pull`命令加镜像名可以拉取镜像。
docker pull 名称:版本，latest表示最新，也可以自己指定版本。比如MySQL:5.7

`run`可以立刻启动容器。-i选项是     
如果仅仅只是想根据镜像创建容器而不现在就启动的话，可以使用create命令。
通过Create命令创建容器之后，每个容器在创建后都有一个属于自己的ID，相当于**面向对象中的实例化**。

`ps`可以查看容器状态，就和linux的命令一样，`-a选项可以查看所有的容器，其他的选项可以通过-h查看帮助。

`start`可以启动处于停止状态（之前创建过）的容器，带一个名字或者容器id，**id可以不写全，但是要保证唯一**

stop停止容器

rm删除容器。
容器的所有数据是保存在容器内的，所以如果使用rm指令删除容器之后，数据也会消失
并且有这么一个需求，如果你想在容器**停止后立刻就删除**的话（他确实是有这种需求）那么你可以在容器运行的时候添加一个选项 `--rm`

images可以查看所有存在的镜像。

rmi对应的可以删除镜像。

## 镜像结构
在打包一个项目的时候，通常都需要一个环境。
> 比如说你需要安装数据库，需要安装缓存，在这样的需求下，纯操作系统的镜像就称之为base镜像项目；实际用于生产环境的镜像都是基于base镜像定制后打包的。 
> 这种方式就相当于你在JAVA做架构操作的时候设置一个base类一样的。这方面你进行统一管理，属于一种复用策略。

这里以centOS镜像为例。

通过命令`docker pull centos`拉取一个centOS镜像

**base镜像的机制。**
基于宿主操作系统；

**Linux的内核空间的机制**
这里先大致介绍一下Linux的空间：他有内核空间和用户空间构成。内核就是核心。Linux启动后一般会先加载boot fs文件系统。加载完成后自动卸载，然后加载用户空间的文件系统。

就是因为Linux它是大的来说是分成两层：**系统层和用户层**，而我个人的看法是Docker开始是针对Linux开发的，所以它的底层架构也跟Linux类似。

base镜像**依附于宿主的内核**。
uname命令可以查看内核版本。

run命令中的i参数表示在容器上打开一个标准的输入接口，也就是stdin，T选项表示分配一个伪终端或者说小型终端。 这样你就可以像是和物理设备中一样，在dock中使用终端了。


从视频的笔记中可以看出多卡的内核版本确实是**依附于宿主操作系统的内核版本的，所以如果你的软件对内核有额外要求，那docker可能就派不上用场了。
不过**这种场景基本就没有**吧，所以没有必要考虑这个。


现在大部分的镜像基本都是基于base镜像制造出来的，并且docker制作镜像是基于那种层次化结构的。
这样多个容器可以在不同的层次自由拼装。
呃，我的理解是呢，就是，一份镜像根据层次的不同可以启动多个不同的容器，它们都是基于一个镜像制造的。

为什么要这么设计呢？就是一种**复用策略**。
Docker采用的是**联合文件系统**，文件是叠加的，如果不同层次中存在于相同位置的软件，那么**上层的就会覆盖掉下层**。
而修改的时候**不会直接对镜像进行修改**，而是在最上面的容器层进行修改。这么做的目的也是为了实现多个容器共存，**后来的不会影响前面的**。

所以在这种文件结构下，各个操作的细节如下：

- 文件读取：要读取一个文件，Docker会最上层往下依次寻找，找到后则打开文件。
- 文件创建和修改：创建新文件会直接添加到容器层中，修改文件会从上往下依次寻找各个镜像中的文件，如果**找到，则将其复制到容器层**，再进行修改。
- 删除文件：删除文件也会从上往下依次寻找各个镜像中的文件，一旦找到，并不会直接删除镜像中的文件，而是在容器层标记这个删除操作。

另外， docker是带有**缓存机制**的，同一个镜像，如果做了轻微修改之后，进行二次构建的话，那么速度会变得非常的快
原理的话，我推测是因为 docker的分层机制，每一层的内容如果不发生变化的话，那么就直接复用，速度当然快了。
另外，不止 commit， pull也有缓存机制。

并且， **似乎是**只有通过 dockerfile构建的镜像才会有层次记录（每层镜像层都有对应的id），而commit的没有。

关于 dockerfile的命令，官网有详细的命令解释，不容错过。
以上就是构建镜像的基本内容。这是最基础的知识。


# 仓库
类似于GitHub这种远程的Git的仓库， docker也是有仓库的，我们可以将自己的镜像上传到这里：https://hub.docker.com/repositories，没有账号的先去进行注册。

有了一个仓库之后，就可以往这个仓库里面去上传你的自定义镜像了。

从他们的这个界面来看的话，创建一个新的仓库似乎只需要定义名称、描述和是否公开这几个属性就可以了。

总之，远程的东西弄好之后，你就要考虑去准备对应的镜像，并考虑把他上传上去了。

首先最好给你的镜像起一个规范的名字。
你可以通过tag命令来，重新给镜像打标签。
docker tag ubuntu-java-file:latest 用户名/仓库名称:版本
这里有用户名称是因为你在dockerhub上面是是用户名，仓库名这样命名的。

然后如果你想象官方仓库里推送镜像的话，你首先需要登录，然后才能推送，和GitHub是一样的。

docker login -u 用户名 -p 密码
这个选项和MySQL的差不多。
登录成功之后，日志的最下方会提示succeed。

登录成功之后，你就可以通过push指令来把你的镜像推送到远程仓库了。
通过push指令，拥有补全功能。把你的镜像推送上去就可以了。可以通过选项来指定不同的仓库，如果不指定的话，默认就是dockerhub

推送时它会有日志提示，你可以清楚的看到不同镜像层的ID。

推送成功之后，他会给你返回一个sha256函数加密的之后得到的字符串

之后你去dockerhub对应的仓库页面查看镜像的详细信息后，它会给你显示镜像的架构，压缩后的大小，最后一次推送的时间以及镜像的层次结构。（Layer）

之后你就可以测试仓库里的镜像是否正常了，通过search命令可以在远程仓库中查找镜像。

dockerhub也可以搭建私服，不过他这里没有介绍。到时候如果需要实际使用的话，肯定是要进行加速或者自己自定义服务器的肯定是有的。那就到时候再说。


# Spring Boot项目打包成docker镜像发布到远程服务器上
下面来一个spring boot打包成docker镜像的实战。实际上在我以前学JAVA web的时候如果可以找到这个教程就好了。

因为这里是只是示范通用的打包流程，所以做示范的时候会尽可能的简单。
首先你需要创建一个spring boot项目，选择JAVA 8版本。，Maven的依赖项的话，只需要勾选web项目的spring web即可。

不需要对项目做太多的修改，你只需要让他能够正常运行就可以了。
理论上在远程的Linux环境下也是可以通过maven打包的，不一定需要idea。在我自己电脑上跑idea总觉得很吃亏，不过这里暂时留一个悬念，以后可能会考虑自己动手尝试一下。

在项目的根目录下创建一个dockerfile，然后类似于之前怎么在Linux里面写的，现在就怎么在这个里面写。

补充： 宝塔面板需要安装openjdk8的话，命令是`sudo yum install java-1.8.0-openjdk`
但是运行我自己电脑上idea的程序失败了，不知道为什么


由于idea集成了docker插件，所以你也可以在这里尝试一下idea的打包方式。
你可以很明显的在dockerfile的行号那里发现一个明显的启动按钮，那里可以配置你的多可服务器选项。默认为空，你可以点击加号添加一个docker服务器，选择远程服务器（Tcp套接字），然后先去你的Linux服务器上配置一下docker，

打开docker的服务配置文件：
sudo vim /etc/systemd/system/multi-user.target.wants/docker.service

在service标签的地方，ExecStart那个key的位置。
有一个H，fd，类似于某种url链接的地方，呃，在这个东西的位置后面空上一格填写H选项，Tcp为协议，然后四个零。表示任意外网都可以连接。

`-H tcp://0.0.0.0`
修改完成后保存，然后重启服务：
sudo systemctl daemon-reload
sudo systemctl restart docker.service
啊，不知道这个命令能不能在ubuntu下生效

然后需要在云服务器的控制台开启2375的tcp端口，就像我开求生服务器的时候也需要开放端口一样。

然后回到Idea，填入tcp开头的协议地址就可以了。

tcp://ip:2375

然后服务器就跟你的idea绑定好了。运行配置好之后呢，指定dockerfile文件的路径和镜像的名称以及标签。

然后点击运行，它就会在你的远程服务器上构建出一个新的镜像了。
这是一个带有JAVA环境的ubuntu镜像。对于spring boot项目来说，这就是他的base镜像。
接下来就把spring boot的项目打包，然后在容器启动时运行就可以了。
打开maven的窗口，选择package来进行打包。
完成之后可以在根目录的target目录下找到包好的jar包。
现在jar包还没有和容器产生关系，在file中通过copy命令可以把容器外的文件复制进容器中（应该是）这里涉及到容器的存储管理问题。以后再说。

如何让容器启动后执行某个命令呢，可以用cmd指令，他的作用就是在容器启动后执行某段命令。

这里有一个有意思的内容，似乎spring boot的项目只需要用maven打包之后，然后在有JAVA的环境下用java -jar，然后是jar包的名称就可以运行了。 而原版的spring似乎是不可以这样。

另外通过expose命令可以使容器向外暴露端口。docker容器内部的网络默认和外面是隔离的。这和后面的容器网络内容相关。现在先记着。Docker的p选项可以把容器内的端口绑定到宿主机的端口上。

选项d表示在后台运行。

另外idea本身的docker插件就是十分强大的。你可以把它当成docker的图形化客户端来使用。

不过为了方便使用，你最好还是把他们推送到官方的仓库上。 毕竟它是免费的不是吗？

接下来演示IDEA中上传镜像到dockerhub的过程：
右键你想要上传的镜像，选择推送。
你可以编辑与docker register相关的信息。把它填写好就行。

docker network ls

容器的网络管理

这部分属于和多个底层相关的内容了。熟悉之后可以更好的了解Docker的底层原理。

在你第一次安装完docker之后，多会在你的主机上或者说宿主机上创建三个网络，可以使用命令：docker network ls
Bridge是桥接模式，Host是宿主模式。跟虚拟机上的网络配置是差不多的。第三个这里up主没提，那我也不管了。

直接输入docker network命令，可以查看它可用的命令。等同于选项h。

进行网络测试之前，首先要进行一下环境的配置。首先通过终端启动unbuntu镜像，docker run -it ubuntu因为默认的unbuntu是什么都没有的，所以需要进行一下配置。

在执行这个命令来安装对应的网络测试软件。
apt update
apt install net-tools iputils-ping curl

安装完毕之后退出容器终端，并通过docker commit lucid_sammet ubuntu-net构建新的镜像。

这里回顾一下命令，可以把以指定的容器为模板构建一个新镜像。

none网络，这个网络除了有一个本地环回网络之外，就没有其他网络了。我这里认为这个所谓的网络是指整个多个中可以选择的网络，而不是容器内部的网络，如果要为容器内部指定网络，可以通过启动容器时选项network来指定。比如docker run -it --network=name ubuntu

进入到终端后，通过命令ifconfig查看网络配置。可以发现它是一个本地回环网络内网地址是127.0.0.1。换句话说，他与外界是隔绝的。如果你需要你的容器实例断网，你就可以选择这个网络。

Bridge网络，也是你在不指定网络时默认使用的网络类型。之后如果你在容器外的终端通过命令来查看网络状态的话，你可以发现宿主机上多了一张名字叫docker0的虚拟网卡。他就是由创建的docker创建的。

那这个设备有什么用呢？首先你可以看到他的IP地址的末尾是1，然后打开容器终端，再次通过命令查看网络配置，你可以发现容器内部它的IP地址是2。这实际上就是docker创建的虚拟网络。就像容器单独插了一根虚拟的网线。
个人的感觉，这个就像是go语言的gmp模型的那个用于负责调度的g0。换句话说，docker0就好比docker容器的网络所使用的路由器。他与宿主机的网络进行连接。

可以通过命令docker network inspect bridge来查看网桥的配置。
可以看到日志打印出的消息是一大串json字符串，里面就是关于这个网络的详细信息，由于里面涉及到比较多的网络的知识，我这里说不明白。

如果在创建容器的时候，指定网络为host，那这就意味着容器内的网络是会共享素质主机的网络的，也就是说他们的网络配置是完全一样的。此时的容器，从网络的层面来说，可以说就是宿主机的一个应用程序。（这里是我个人的看法。）

在共享网络的时候，网络传输的性能是最高的，并且可以直接开放端口，不需要任何桥接。

似乎是懒人的最佳方案。不过默认的配置仍然是桥接，你需要手动指定。

此外，你还可以定义自己的网络配置。那我的看法就是上面那三种其实就是默认的网络配置了。

Docker默认提供三种网络驱动：bridge、overlay、macvlan，不同的驱动对应着不同的网络设备驱动，实现的功能也不一样，比如bridge类型的，其实就和我们前面介绍的桥接网络是一样的。通过选项--driver可以指定驱动。

可以使用network create来试试看：
docker network create --driver bridge test

在这里提一嘴，docker虽然可以创建自定义网络，让我感觉个人大部分情况下应该都是用不上。

注意！不同的网络之间是彼此隔离的

这里有一个小知识点。如果你想退出容器终端而不结束容器实例的话，你可以按control加p加q，这样容器就不会结束，只是退出了终端页面。通过PS命令可以查看它的运行状态。

如果你有需要，可以通过命令docker network connect test 容器ID/名称，连接到另外一个容器所属的网络下。

这里up主还有额外展示了一个命令attach，就是可以重新连接回容器的终端。这个attach直接翻译的意思就是附加到。


还是不得不感叹一句，这里跟网络相关的知识真的很多。

容器间的网络通信
如果在创建容器的时候，指定的网络是同一个网络，那么他们就属于在同一个子网下相互间就能ping通，你可以启动两个容器测试一下

如果要保证容器之间可以通信，只需要保证两个容器处于同一个网络下即可，不过你无法提前得知他们的IP地址。也可以通过--ip来指定，但是还是比较麻烦。

所以类似于DNS的原理，docker也提供了一套类似的机制来帮助使用者快速的连接到不同的子网。 只需要指定容器名字，然后容器之间就可以通过名字来拼通了，就像DNS服务器一样。

现在不是你需要提前确定IP地址了，现在是你根本就不需要知道IP地址，这就是DNS服务器做的事情，非常的方便。

也可以让两个容器同时共享同一个网络，类似于之前的那一个宿主模式，在网络选项那里指定--network=container:你想要共享的网络的容器的名字

怎么跟你说明这个共享关系呢？你在容器里面启动一个nginx，然后你访问容器二的网络服务，它是可以访问到的。




# 容器外部网络
docker默认的三种网络只有none的网络是单机模式。共享模式很好理解，它就是**依靠的宿主机的网络**，这里主要来讲一下桥接模式。

调节模式主要是依靠nat（Network Address Translation）服务来与外界网络进行的通信。 它的原理是：将地址进行转换，再利用宿主主机的IP地址发送数据包出去。 他主要讲的就是内网跟外网之间如何通信的问题。

下面是关于NAT原理的东西。
首先是局域网的IP地址。一般为192.168开头。他是没办法直接去访问外网的。

通过NAT，可以实现将局域网的IP地址，映射为对应的公网IP地址。
我的理解就是：公网才能传输数据报到互联网上面去。那么内网的地址，映射成对应的公网地址，你就可以把内网当成公网来用了。

由于公网IP地址有限，所以一般采用IP+端口结合使用的形式ANPT。
这里还是吐槽一下国内的既得利益者们，ip地址显示普及的比ipv6快，政治高于一些，哈哈哈哈哈哈哈哈

实际上我们家里的路由器一般都带有NAT功能，默认开启NAT模式，包括我们的小区也是有一个NAT设备在进行转换的，这样你的电脑才能在互联网的世界中遨游。当然NAT也可以保护内网的设备不会直接暴露在公网，这样也会更加的安全，只有当我们主动发起连接时，别人才能知道我们。

docker采用了类似于 NAT的原理。

容器内的数据报，发送给docker建立的虚拟子网的桥接器，然后通过nat到达宿主机，最后宿主机把你的数据报发送到互联网上去。

不过，你看前面提到的，为了安全性， nat只要在“我们”主动向外界联系的时候，才会暴露地址；如果docker采用类似的原理，而当容器内部署的是某种需要外界主动向容器内部发起连接的时候，那么该怎么办呢？

还记得前面的那个“端口映射吗”，自己主动配置端口映射宿主机端口和容器端口，这样，当访问宿主机的这个端口的时候，就相当于访问容器内部了。（直接转发到容器的内部）
`docker run -d -p 80:80 nginx`

输入docker ps也可以查看对应的情况。

以上，就是 docker的关于容器网络的内容，但是只是一部分，后面会继续补充。

# 持久化存储
进度，容器的持久化存储。

笔记的记录不再依赖手机的语音输入。

回忆一下docker使用的联合文件系统：它针对容器的修改，都是在最顶层的容器层实现的，不会对容器层下面的镜像层进行修改，这样做是为了不破坏镜像（因为可能还要基于这些镜像来创建新的容器的。）

不过在某些情况下，我们会希望对这些文件进行实际的修改，此时便产生一个新的需求：持久化。

在某些情况下，我们可能希望对容器内的某些文件进行持久化存储，而不是一次性的，这里就要用到数据卷（Data Volume）了。

先准备需要容器环境。
```sh
docker run -it ubuntu
apt update && apt install -y vim
```

实现持久化的一种方式是，容器把文件保存在宿主主机上面，然后当需要时，从宿主主机上读取这个文件即可。

通过-v选项可以把宿主主机上的文件挂载到容器内部。这样在容器内部就可以去访问了。

例如：
```sh
# -v选项将宿主机上的test目录挂载到容器内的root/test路径下
docker run -it -v ~/test:/root/test ubuntu-volume
```

这样我们就可以直接在容器中访问宿主主机上的文件了，当然如果我们对挂载目录中的文件进行编辑，那么相当于编辑的是宿主主机的数据。
有点类似之前的共享文件夹。

UP这里，不同于我本人的，用到了一种较为常见的场景：nginx代理前端的页面：
比如我们现在想要部署一个Nginx服务器来代理我们的前端，就可以直接将前端页面保存到宿主主机上，然后通过挂载的形式让容器中的Nginx访问，这样就算之后Nginx镜像有升级，需要重新创建，也不会影响到我们的前端页面。这里我们来测试一下，我们先将前端模板上传到服务器
```sh
# 谢在这里只是用来做一种记录
scp Downloads/moban5676.zip 192.168.10.10:~/
```

这里用到了一个scp命令（Secure Copy），是linux之间用于传输数据的一种方式。

一句话总结：就是通过v选项可以在每次启动容器的时候来指定对应的目录。

把前端文件放到nginx默认的前段文件的目录里面，这样nginx就可以自动代理你放进去的前端文件。可以实现前端的分离。

然后继续是关于挂载的内容：
前面的-v选项是指定了宿主机的目录的；如果你不指定这个目录的话，只指定容器内的目录，那么docker就会自动的在你的宿主机上创建一个目录，然后跟前面的内容一样。

比如：
注意如果我们在使用-v参数时不指定宿主主机上的目录进行挂载的话，那么就由Docker来自动创建一个目录，并且会将容器中对应路径下的内容拷贝到这个自动创建的目录中，最后挂在到容器中，这种就是由Docker管理的数据卷了（docker managed volume）我们来试试看：

docker run -it -v /root/abc ubuntu-volume

运行上面这个命令，进入容器终端后，通过ls命令查看可以发现容器是有这个在启动时指定的目录的。

不过既然是挂载，那么对应的宿主机的目录是哪个呢？

依旧可以通过inspect命令来查看：docker inspect bold_banzai

可以发现指向的是位于var/lib中的某个目录，这个相当于docker文件管理的默认值了。

在对应的mounts的位置可以找到挂载的位置。（这里提示一下，这里显示的容器的细节依旧是json格式的string）

而且如果你要进去这个目录的话，还需要比较高的权限，他这里通过sudo -s的命令不知道什么意思。

这样做的目的是为了在宿主主机上永久的保存数据，这样容器和数据可以隔离开来，我觉得云原生的应该也是提倡这种理念的。

复习一下：attach命令可以回到docker容器的终端。

以上这些持久化的操作，在 docker里面统称为数据卷（volume的操作）他也是有对应的命令的：例如：


docker volume ls可以查看数据卷的状况，就像linux的ls一样。
docker volume rm 数据卷的id还是名称，就可以移除对应的数据卷。

当然，这种持久化的措施才需要通过数据卷，如果单单只是一次性的在容器上使用这些数据的话，是不需要这么复杂的；记得前面使用过的一个cp的命令吗？它可以将容器外部的文件直接复制到容器的内部去。

docker cp 你想要复制进去文件的路径 容器名称:对应内部的目录的路径

cp命令支持容器内外双向复制文件。
如果是从容器里面复制文件到外面去的话，就把容器名字输在前面就可以了。

# 容器间的数据共享
首先不考虑新内容，如果用上一章了解到的方法，要在容器之间共享文件的话，就直接在容器外部创建一个 **公共的目录就可以**。

之前提到过，如果在使用v选项进行挂载的时候没有指定容器外部的目录的话，那么 docker就会自动的来管理这个数据卷。

可以通过指令 docker volume ls来查看当前数据卷的情况。
docker volume inspect 数据卷的id（应该是名字，因为使用docker的时候print出来的是显示的 volume name）来查看对应这个数据卷的详细信息。
！！注意这个指令只能显示由docker进行管理的数据卷的信息，如果是在容器启动的时候自己手动指定的，那么对应的就不会显示它的信息了。


数据卷与容器彼此分离，如果一个容器挂载了一个数据卷，当这个容器被手动的删除之后（使用rm指令），这个数据卷是不会收到影响的。

当然，也可以通过对应的rm指令来删除它。

如果想要创建一个新的，由 docker来进行管理的，那么就可以通过create命令来创建一个数据卷。
现在一看，通过-v命令指令的那个共享目录可能就是告诉 docker不要多管闲事的意思...

如果在容器启动的时候要挂载自己创建的数据卷的时候，按照之间那种方式执行，只不过这次的参数由路径换成一个非路径的名字（就是不带任何斜杠）了。

docker run -it -v lbw:/root/test ubuntu-cp

此外， docker还支持直接复用已挂在文件的目录：通过--volumes-from=容器名称 来执行容器，这样就直接使用这个容器指定的挂载目录了。
这种用来给其他容器提供挂载目录的容器被称为挂载容器。很像面向对象中继承。

这样，我们基于共享文件夹方式的容器间的文件共享的方式就算是实现了。

不过，重新回忆一下我们的目的：实现容器之间的数据共享；而我们前面的那种方式，是需要显式的需要宿主机参与其中的。（不可能脱离宿主机）但是，我们希望，至少在使用的层面，我们不希望宿主机参与进来，降低耦合度。
我们可以通过这样一种方式：
其实我们可以将数据完全放入到容器中，通过构建一个容器，来直接将容器中打包好的数据分享给其他容器，当然本质上依然是一个Docker管理的数据卷，虽然还是没有完全脱离主机，但是移植性就高得多了。

首先来编写一个dockerfile
```dockerfile
FROM ubuntu
ADD moban5676.tar.gz /usr/share/nginx/html/
VOLUME /usr/share/nginx/html/
```

在dockerfile中， ADD命令和cp命令类似，也是用于复制文件到容器中的（From host to container），但是他会对压缩文件进行自动解压缩。

然后VOLUME可以用来设置挂载点，就像-v选项一样。

使用tar命令可以进行压缩和解压缩：
这里演示的是压缩的命令： 
```sh
cd test
tar -zcvf moban5676.tar.gz *
mv moban5676.tar.gz ..
cd ..
```

接着就通过这个dockerfile来构建镜像了。
回忆一下： docker build -t data .
-t选项是执行镜像的tag的名称；这里应该就是找到当前目录的dockerfile，然后tag的名字为data

说实话这里有点没看懂...他到底是怎么做的呢...?
不过算了，到时候有需要的时候再来回顾好了。

# 扩展：Docker其他常用命令
通过 docker log 容器id可以打印容器内部的终端日志信息；
-f参数可以持续打印。

现在我们的容器已经启动了，但是我们想要**进入到容器监控容器的情况**怎么办呢？我们可以是attach命令来附加到容器启动命令的终端上：

不过，如果只是想退出终端而不结束容器内运行的程序的话，需要按cp然后cq，不能直接cc，否则内部的程序会直接结束的。。。

如果要另外启动一个终端（比如在已经启动一个SpringBoot项目的情况下，你要新开一个终端去干别的事情，就像screen一样）
例如
```sh
docker exec -it 容器的名字 bash（启动它的终端）
```

输入kill命令可以强制停止容器：
比如
```sh
docker kill 容器的名字
```

输入pause命令可以暂停容器，相当于把容器给冻结了，但是未来的某个时间点它又可以恢复：
```sh
docker pause 容器的名字
unpause
```

# 物理资源限制
在大部分情况下，我们是不希望容器占据所有的系统资源的（或者说不希望它无止境的扩充）
比如说一个容器，我只希望给他分配2g的空间；

docker run -m 内存限制 --memory-swap=内存和交换分区总共的内存限制 镜像名称

其中-m参数是对容器的物理内存的使用限制，而--memory-swap是对内存和交换分区总和的限制，它们默认都是-1，也就是说没有任何的限制。

除了内存之外，也可以对cpu进行限制：
当然除了对内存的限制之外，我们也可以对CPU资源进行限额，默认情况下所有的容器都可以平等地使用CPU资源，我们可以调整不同的容器的CPU权重（默认为1024），来按需分配资源，这里需要使用到-c选项，也可以输入全名--cpu-share

可以安装压力测试工具，通过apt安装stress就可以了。

...这部分的内容如果有需要去博客里面查一下就可以了。

# 容器监控
对容器的资源占用情况进行监控，可以使用status命令
可以实时对容器的各项状态进行监控，包括内存使用、CPU占用、网络I/O、磁盘I/O等信息，当然如果我们限制内存的使用的话，也是可以看到对应的情况的。

这里回顾一下，docker命令的输出结果可以作为参数传递给其他的docker命令，例如
`docker rm $(docker ps -aq)`

除了内存磁盘这些的数据资源外，也可以查看对应的进程的情况：
`docker top`用法几乎和linux中的top一样。

有时候你可能觉得这种通过控制台日志来进行调试的方式没有gui的那么方便，那么可以通过网页来查看样式更加精美的ui，比如 docker自身就支持通过一个Portainer容器，运行它就有对应的效果了。

具体的使用方法请看博客。

可以理解为docker的一个控制面板。

# 容器编排：DockerCompose
```
我这里了解compose主要是为了部署那个音趴的服务。
```

比如现在我们要在一台主机上部署很多种类型的服务，包括数据库、消息队列、SpringBoot应用程序若干，或是想要搭建一个MySQL集群，这时我们就需要创建多个容器来完成来，但是我们希望能够实现一键部署，这时该怎么办呢？我们就要用到容器编排了，让多个容器按照我们自己的编排进行部署。 

简单地说，就是用来管理容器的一个管理员。他负责让多个容器形成一个整体，和谐有序。

要系统的学习的话，推荐看官方的文档：
[Link](https://docs.docker.com/get-started/08_using_compose/)

## 安装
DockerCompose这玩意的安装独立于docker的安装，在 ubuntu环境下可以通过命令 `sudo apt install docker-compose-plugin
`安装。

以部署一个完整的 SpringBoot项目为例：
现在需要为它准备一个 MySQL服务器和 redis服务器；
先拉取他们的镜像：
```sh
docker pull mysql/mysql-server
docker pull redis
```

要在本地进行操作的话，还需要在本地下载对应的客户端：[Download](https://github.com/docker/compose/releases)

然后在IDEA的docker那里配置 DockerCompose的路径；

MacOS似乎有额外的流程，如果有需要可以去看博客。

配置好上面的部分后其实就已经可以正常使用了。

他也有配置文件，名字叫docker-compose.yml

注意，它的右上角有一个同步的图标，需要把它设置成不与服务同步，服务就是左下角的那个标签那里，可以调出来。

现在可以开始配置了。

现在需要三个：一个是 SpringBoot，一个 MySQL，一个 redis，需要分别三个容器运行（这可以理解为解耦合的一种，总之比较复杂。）
一个基础的配置如下：
```yml
version: "3.9"  #首先是版本号，别乱写，这个是和Docker版本有对应的
services:   #services里面就是我们所有需要进行编排的服务了
  spring:   #服务名称，随便起
    container_name: app_springboot  #一会要创建的容器名称
  mysql:
    container_name: app_mysql
  redis:
    container_name: app_redis

```

因为 SpringBoot需要进行更新和修改的关系，所以要用 dockerfile来构建，好像是这么个逻辑；

并且 SpringBoot项目还需要映射端口，

```sh
version: "3.9"  #首先是版本号，别乱写，这个是和Docker版本有对应的
services:   #services里面就是我们所有需要进行编排的服务了
  spring:   #服务名称，随便起
    container_name: app_springboot  #一会要创建的容器名称
    build: .
    ports:
    - "8080:8080" # 前面这个减号代表这是一个列表，说明以后可以添加多个端口
  mysql:
    container_name: app_mysql
    image: mysql/mysql-server:latest
  redis:
    container_name: app_redis
    image: redis:latest

```


现在，一个最基本的 compose的配置文件就写好了，可以进行一键部署了。 在idea的服务标签那个地方，直接找到部署按钮，就可以进行部署了。

已经变成蓝色的鲸鱼图标表示可以正常运行了。

目前为止，总结一下，通过一个配置文件，就可以快速的完成容器的编排工作，不算复杂。

以上是在idea下的操作，简单直观。如果在命令行下，编写好配置文件，然后通过命令 docker compose up命令，它会自动的寻找当前目录的yml文件，然后部署。

并且此时打印出来的日志信息，不同的容器会用不同的颜色来进行标注。

# 一个完整的DockerCompose实践
可以看博客，我暂时没有这个需求，应该
再贴一次博客地址：