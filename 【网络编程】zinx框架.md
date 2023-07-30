通过视频学习。

这是一个轻量级的、TCP层级的网络框架。
课程本身主要是分为两个部分：框架学习和基于框架完成一个服务器应用程序的搭建。

而且我很喜欢作者的一句话：任何软件都是逐渐迭代出来的。

# 效果演示
演示了一个unity游戏和服务器搭配使用的情况。

这里再次提醒了我实现一个看代码的一种方式：
一定是要带着目的去看的。不然就像跟图的遍历一样会迷失方向。
一上来就去看main这种其实是不太适合的。

# 实现一个基本的服务器
## 模块定义
根据Go编码的**鸭子类型**风格，通常是使用 **接口 --- 实现类**的架构风格来进行编码。
就是根据需求，思考它的模块结构，比如抽象出接口和具体的实现。
而一个服务器，最重要的就是它的三个生命周期的方法。

## 服务器启动的基本过程
服务器一般就是监听指定的位置（IP:Port），获取网络套接字句柄（与别人相连！），然后通过io提供的函数进行io操作。
所以一个服务器实现类，通常会有这些字段：
Name 表示服务器的名称，总该有个称呼
IpVersion 这里应该更像是网络的协议，tcp4或者tcp6或者websocket之类的。
ip 监听的ip，当从这些ip地址过来的时候，需要相关的信息；
port 端口就不解释了。


如果从OOP的方式来编程的话，一般会用New方法来创建一个对应的实例：
```go
server := NewDefaultServer("[zinx v0.1]")
server.Serve()
```


通常一个服务器程序的启动，需要这些步骤：
1. 获取网络通信的套接字，在go中就是一个TCPAddr，它是一个包含ip和port的struct。理论上，有这两个东西就可以进行网络通信了。
2. 然后用listener进行监听。


单单从数据类型来讲，就是 Addr -> Listener -> Conn
go中一个tcp连接的过程如下：
```go
	// Progress of Receive a TCPConn
	// 1. 获取一个tcp的连接对象：TCPAddr
	tcpAddr, err := net.ResolveTCPAddr("tcp4", "0.0.0.0")
	if err != nil {
		return
	}

	// 使用函数获取监听器；
	listenner, err := net.ListenTCP("tcp4", tcpAddr)
	if err != nil {
		return
	}
	// 进行Accept()监听，获取net.Conn对象
	for{
		conn, err := listener.Accept()
		if err != nil {
			continue
		}
		
		// Handle TCP conn
		go func() {
			
		}()
	}
```

其中，这个conn类似于Java的IOStream，可以使用read和write来进行读写操作。
例如，在获取到一个conn连接之后，使用buf和read和write进行回显。

总之，Conn是一个接口（类似于Java中的流，拥有read和write的方法），都是通过一个byte slice来写入和读取的。

然后一般写服务器程序的话，需要使用客户端程序来进行一个测试；可以抽象一个client
使用net.Dial这个方法来获取一个连接对象，然后用read和write方法。

总之不管是服务端还是客户端，都是通过net.Conn这个接口来读写数据的。


代码实现到这里可以进行一次git的提交：
完成Server的抽象和实现，并在cmd中用实际的Server和Client来进行测试；


## 网络链接的封装
我现在的理解就是它是跟net.Conn这个接口是差不多的，是在标准的网络连接套接字上的一层再封装。

一般会设计成一个接口，一般包括获取各种核心字段句柄的方法，以及发送信息的方法，处理业务逻辑的接口。

### 一个具体的实现
比如以下是一个实际的IConnection的实现：

类似切片是对数组的抽象，IConn实际上是对Conn的抽象，所以他需要有一个底层的Conn；

Conn net.TCPConn

这个链接是否是已经关闭的：
isClosed bool

用来告知当前的Conn需要关闭了，go里面经常使用一个channel来实现，不知道其他语言是怎么做的；
ExitChan <-chan struct{}

并且需要与业务相绑定，这里可以直接使用一个函数类型：type HandleFunc

# 全局配置
这里好像是使用json的东西来配置。 常见的配置文件格式包括xml（老项目例如java spring项目以前的，一部分net framework），json（常见于前端），toml（rust cargo的配置文件）,yaml（spring boot，go相关的框架比较常见。）

它这里好像不讲go是怎么来解析go文件的。
不过反正go有相关的标准库之类的东西。


这里写一个怎么用go来解析json的模板代码（demo吧）
一个简单的步骤是读取json文件并获取对应的字节数据，然后使用json.Unmashrall函数来实现字段的映射。

```go
data, _ := os.ReadFile(filePath)

toBeFilled := new(User)

err = json.Unmarshal(data, toBeFilled)
// if err != nil ...

```

# 解决TCP粘包的问题
一个TCP的报文通常是由一个头部和body组成。前者是一些meta data, 后面基本是数据。

粘包这个概念其实很好理解：本来是两个数据报因为一些原因被当成一个来识别了，就好像他们粘在一起了一样。
可以理解成是一个数据的边界问题之类的。

有一种王道的解决方式（我觉得这个其实就基于贪心）：
就类似于Http中Req和Res的封装类似。
好像是把这种封装方法称作TLV（type， length，value）格式。
指定读取长度，偏移量就好确定了。

类似于一个byte的数据是
type + length + data这样的样子。

定义好格式之后，就是一个序列化（byte切片）和反序列化（体现在go里面是一个struct）的格式。
并且定义好相应的转换方法。

封包：Header信息写入； data信息写入；
拆包：先读取Header信息，然后根据Header信息决定相关的操作或者业务之类的。

```go
func Pack(message *IMessage) []byte {
	var dataBuff = bytes.NewBuffer([]byte{})
	binary.Write(dataBuff, binary.LittleEndian, data)
}
```

就是利用这样一个方法来制作一个字节数据信息。


代码干脆不写了。反正大概是这么一个样子。

