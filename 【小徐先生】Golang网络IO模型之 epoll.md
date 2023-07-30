Golang底层网络模型是epoll，这是一种使用广泛的IO模型，在Linux也是以此为基础（Windows似乎不是）

本文章主要分为三个部分：
1. 何为IO多路复用
2. 何为epoll
3. 串联go底层的源码

# IO多路复用
章节路径树
![[Pasted image 20230706182002.png]]
## 多路复用概念
首先拆解多路复用一词：

- • 多路：存在多个待服务的对象
- • 复用：只由一个执行单元提供服务

核心应该在于复用的实现。
打个比方：多名顾客在餐厅内用餐，考虑到经营成本，很难做到为每名顾客单独提供一名招待员作一对一服务，因此餐厅经理安排每名服务生固定负责几个餐桌，服务生在几个桌次间来回辗转提供服务，这个过程本质上就是一种多路复用.


## 多路复用的简单实现
首先来到一些皆文件的Linux系统中，多路就是存在多个文件句柄（file descriptor）
而复用的则是一个用于处理fd的thread，被称为loop thread。

一个thread服务于一个fd，这个好理解；但是问题在于如果在多个fd同时都有需求的时候，如何让顾客产生不被冷落的感觉，这是一个难点。

我也来写一下伪代码
```go
// list
fds := []fd{fd1, fd2, fd3,...}

i := 0

// loop thread
for {
	fd := fd[i]
	data := read(fd)
	handle(data)

	i++
	if i == len i = 0
	
}
```

## NIO的实现

NIO的关键点在于，始终让thread动起来，而不是被动的因为fd而处于无所事事的状态：
所以根据上面的代码可以进行改动：
```go
...
if data, err := tryRead(fd); err == nil {
	handle(data)
}

sleep()
```

你可能会奇怪为什么这里要加入一个sleep来休眠，这里来引用原文的解释：

>这里确实解决阻塞 IO 中的问题，其本质上是一种非阻塞 IO 模型（Nonblocking IO，简称 NIO）. 但这里仍然存在问题，就是每轮处理之间的休眠时间. 倘若在休眠期间，fd 中有 io event 到达，就无法被正常处理，这同样是一种不好的体验.

这里我觉得直接引入原文的解释是最好的：
通过built-in的机制，让主动权掌握在fd上，实现随叫随到的效果：

那大家可能又问了. 餐厅就不能招个正常的服务生吗，让他在听到客人的招呼时就去提供服务，否则就在一边老实歇着.

没错，这就是正解，设计程序的码农们也是这么想的. 然而实际情况很悲催，在用户态视角下的程序正是哪一个耳目昏聩的服务生，对于 io event 的到达并没有能力做到准确地把握.

于是，这就需要引入操作系统内核的帮助，通过几个内核对外暴露的接口，来进行 IO 多路复用的优雅实现，做到真正意义上的“随叫随到”.


## IO多路复用的优雅实现
![[Pasted image 20230706183904.png]]

所以来看一下最基础的实现：select和最终的版本：epoll。

### select
• 一次可以处理多个 fd，体现多路. 但 fd 数量有限，最多 1024 个
• loop thread 通过 select 将一组 fd 提交到内核做监听
• 当 fd 中无 io event 就绪时，loop thread 会陷入阻塞
• 每当这组 fd 中有 io event 到达时，内核会唤醒 loop thread
• loop thread 无法精准感知到哪些 fd 就绪，需要遍历一轮 fd 列表，时间复杂度 O(N)
• 托付给内核的 fd 列表只具有一轮交互的时效. 新的轮次中，loop thread 需要重新将监听的 fd 列表再传递给内核一次

而epoll不知道通过什么机制，可以实现：
原本需要服务员主动去找顾客才能知道顾客到底有没有需求；
现在一旦顾客有需求，就去叫服务员，避免服务员做没有意义的轮询。

# epoll原理
## epoll指令

这个EPoll就是EventPoll的意思：使用事件来触发回调。这种无论是何时都是很有用的。

EventPoll包含三个指令：
1. epoll_Create：用于开辟空间，创建epoll池子，说简单的就是一个初始化操作；
2. epoll_Ctl：用于池子中fd的增删改的操作
3. epoll_Wait：从对应 epoll 池子中获取就绪的 epollevent，从中可以关联到对应的 fd 和 loop thread 信息

## 核心数据结构
epoll池子使用的是红黑树，它保证所有对节点（Val为epitem）的操作的时间复杂度都在log级别。

而就绪队列的话没有特别说什么，那么就使用一个动态的线性表结构（也就是双向链表）来实现。
![[Pasted image 20230706191947.png]]

## 事件回调的实现
他妈的说了跟没说一样？？？
操作系统内核可以感知IO Event，所以可以避免CPU无意义轮询；
首先，通过内核感知到 io event 事件的动态，令 loop thread 在合适的时机阻塞，避免浪费 CPU；在合适的时机执行，及时处理 io event.

其次，在 io event 就绪时，会精准地将真正就绪的 fd 传递到 loop thread 手中，减少了一次无意义的遍历查询动作.

事件回调的注册是在调用 epoll_ctl 添加 fd 时，此时会提前设置好对这个 fd 关心的事件类型，当对应的 io event 真的发生时，内核会将该 fd 和对应的 loop thread 封装到 epollevent 中，添加到就绪队列 ready list 当中.

之后当用户调用 epoll_wait 时，能够准确地获取到这部分就绪的 epollevent，进而能够将对应的 loop thread 唤醒.


上面是原文。我也不懂有什么意义。

不过我知道一个：红黑树是一种非常重要的树。效率非常的高。



# Golang底层实现
首先贴一个启动tcp服务的伪代码：
```go
// 启动一个 tcp 服务端代码示例
func main(){
   // 创建一个 tcp 端口监听器
   l,_ := net.Listen("tcp",":8080")
   // 主动轮询模型
   for{
       // 等待 tcp 连接到达
       conn,_ := l.Accept()     
       // 开启一个 goroutine 负责一笔客户端请求的处理
       go serve(conn)
   }
}


// 处理一笔 tcp 连接
func serve(conn net.Conn){
    defer conn.Close()
    var buf []byte
    // 读取连接中的数据
    _,_ = conn.Read(buf)    
    // ...
}
```

上面就是