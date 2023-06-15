# go通道
# CSP并发模型（思想？）
这是一种并发模型的方式，go应该是第一个将其发扬光大的语言。
核心思想是：不要通过共享内存来进行通信，而是通过通信来共享内存。
将通信摆到了第一重要的定位。该模型认为如果这样可以使得对并发的思考变得简单。
go的goroutine的设计，就比较贴合CSP的思想。

另外一提，好像 生产者消费者模型也是基于这一思想。

# channel的底层结构
源码：
```go
type hchan struct {
	// chan 里元素数量
	qcount   uint
	// chan 底层循环数组的长度
	dataqsiz uint
	// 指向底层循环数组的指针
	// 只针对有缓冲的 channel
	buf      unsafe.Pointer
	// chan 中元素大小
	elemsize uint16
	// chan 是否被关闭的标志
	closed   uint32
	// chan 中元素类型
	elemtype *_type // element type
	// 已发送元素在循环数组中的索引
	sendx    uint   // send index
	// 已接收元素在循环数组中的索引
	recvx    uint   // receive index
	// 等待接收的 goroutine 队列
	recvq    waitq  // list of recv waiters
	// 等待发送的 goroutine 队列
	sendq    waitq  // list of send waiters

	// 保护 hchan 中所有字段
	lock mutex
}
```
大概的话，其中的字段都和 类型、底层缓冲数组、关联的goroutine，大概就是和这些东西有关。
其中 buf、sendx、recvx这些字段都是**带缓冲的channel（异步）**的goroutine所特有的。
没有缓冲的是同步的，所以上面的那些字段在无缓冲的Channel中没有意义。

其中waitq这个数据类型，表示
> 被阻塞的 goroutine，这些 goroutine 由于尝试读取 channel 或向 channel 发送数据而被阻塞。
它的底层是一个双向链表；


# Channel的创建
go中通过make函数来为特定的数据结构分配空间；
Channel的底层的创建的函数是：
`func makechan(t *chantype, size int64) *hchan`

因此跟slice一样，可以认为Channel的赋值是一种引用传递。

这种创建函数的具体代码：
```go
const hchanSize = unsafe.Sizeof(hchan{}) + uintptr(-int(unsafe.Sizeof(hchan{}))&(maxAlign-1))

func makechan(t *chantype, size int64) *hchan {
	elem := t.elem

	// 省略了检查 channel size，align 的代码
	// ……

	var c *hchan
	// 如果元素类型不含指针 或者 size 大小为 0（无缓冲类型）
	// 只进行一次内存分配
	if elem.kind&kindNoPointers != 0 || size == 0 {
		// 如果 hchan 结构体中不含指针，GC 就不会扫描 chan 中的元素
		// 只分配 "hchan 结构体大小 + 元素大小*个数" 的内存
		c = (*hchan)(mallocgc(hchanSize+uintptr(size)*elem.size, nil, true))
		// 如果是缓冲型 channel 且元素大小不等于 0（大小等于 0的元素类型：struct{}）
		if size > 0 && elem.size != 0 {
			c.buf = add(unsafe.Pointer(c), hchanSize)
		} else {
			// race detector uses this location for synchronization
			// Also prevents us from pointing beyond the allocation (see issue 9401).
			// 1. 非缓冲型的，buf 没用，直接指向 chan 起始地址处
			// 2. 缓冲型的，能进入到这里，说明元素无指针且元素类型为 struct{}，也无影响
			// 因为只会用到接收和发送游标，不会真正拷贝东西到 c.buf 处（这会覆盖 chan的内容）
			c.buf = unsafe.Pointer(c)
		}
	} else {
		// 进行两次内存分配操作
		c = new(hchan)
		c.buf = newarray(elem, int(size))
	}
	c.elemsize = uint16(elem.size)
	c.elemtype = elem
	// 循环数组长度
	c.dataqsiz = uint(size)

	// 返回 hchan 指针
	return c
}
```

就是针对有没有复杂类型，看怎么分配内存，channel的底层还是锁，但是封装的很好；

这个初始化主要是分配内存和gc的问题；代码很复杂，我没有很看懂；

# 阻塞和缓冲，来自chatgpt

不是的，channel的阻塞和缓冲是两个概念，虽然它们都与channel有关。

Channel是Go语言中一种重要的并发机制，用于在go协程之间进行通信和同步。在使用channel时，有时需要考虑到channel的阻塞和缓冲问题。

Channel的阻塞是指当发送或接收操作没有准备好时，当前协程会被阻塞。例如，在一个非缓冲的channel上向一个没有接收者的channel发送数据时，发送操作会被阻塞；在一个非缓冲的channel上接收数据时，如果当前没有发送者发送数据，接收操作也会被阻塞。还有一个例子，当一个非缓冲的channel同时存在sender和receiver时，发送操作和接收操作都会阻塞等待对方的行为。

Channel的缓冲是指channel在被创建时可以指定能够容纳的缓冲区大小。通过给channel设置合适的缓冲区大小，可以在一定程度上避免因为发送和接收操作不同步而产生的阻塞。例如，如果设定了缓冲区大小为n，那么前n次的发送操作都不会阻塞，只有当缓冲区已满时才会产生阻塞；同理，前n次的接收操作也不会阻塞，只有当缓冲区为空时才会产生阻塞。需要注意的是，当缓冲区已满或为空时，发送和接收操作都会阻塞等待对方的行为。

因此，channel的阻塞和缓冲不是同一个概念，阻塞是指发送或接收操作没有准备好时产生的等待行为，而缓冲则是指可以设置的channel缓冲区大小，用于控制发送和接收操作的同步与异步行为。

我的理解是：
**阻塞是一种状态**：是goroutine的状态，由向Channel中发送数据或者从中接受数据时没有对应的发送接收方导致这个操作被阻塞；

而缓冲是Channel的一个属性，他表示channel是否支持缓冲，这样可以避免上面的那些Channel的阻塞的情况；

# 向 channel 发送数据的过程
看《Go 程序员面试笔试宝典》中的说明，channel的核心逻辑就在于“发送”和“接收”的内容，是大头，有大量的代码。

```go
// 位于 src/runtime/chan.go

func chansend(c *hchan, ep unsafe.Pointer, block bool, callerpc uintptr) bool {
	// 如果 channel 是 nil
	if c == nil {
		// 不能阻塞，直接返回 false，表示未发送成功
		if !block {
			return false
		}
		// 当前 goroutine 被挂起
		gopark(nil, nil, "chan send (nil chan)", traceEvGoStop, 2)
		throw("unreachable")
	}

	// 省略 debug 相关……

	// 对于不阻塞的 send，快速检测失败场景
	//
	// 如果 channel 未关闭且 channel 没有多余的缓冲空间。这可能是：
	// 1. channel 是非缓冲型的，且等待接收队列里没有 goroutine
	// 2. channel 是缓冲型的，但循环数组已经装满了元素
	if !block && c.closed == 0 && ((c.dataqsiz == 0 && c.recvq.first == nil) ||
		(c.dataqsiz > 0 && c.qcount == c.dataqsiz)) {
		return false
	}

	var t0 int64
	if blockprofilerate > 0 {
		t0 = cputicks()
	}

	// 锁住 channel，并发安全
	lock(&c.lock)

	// 如果 channel 关闭了
	if c.closed != 0 {
		// 解锁
		unlock(&c.lock)
		// 直接 panic
		panic(plainError("send on closed channel"))
	}

	// 如果接收队列里有 goroutine，直接将要发送的数据拷贝到接收 goroutine
	if sg := c.recvq.dequeue(); sg != nil {
		send(c, sg, ep, func() { unlock(&c.lock) }, 3)
		return true
	}

	// 对于缓冲型的 channel，如果还有缓冲空间
	if c.qcount < c.dataqsiz {
		// qp 指向 buf 的 sendx 位置
		qp := chanbuf(c, c.sendx)

		// ……

		// 将数据从 ep 处拷贝到 qp
		typedmemmove(c.elemtype, qp, ep)
		// 发送游标值加 1
		c.sendx++
		// 如果发送游标值等于容量值，游标值归 0
		if c.sendx == c.dataqsiz {
			c.sendx = 0
		}
		// 缓冲区的元素数量加一
		c.qcount++

		// 解锁
		unlock(&c.lock)
		return true
	}

	// 如果不需要阻塞，则直接返回错误
	if !block {
		unlock(&c.lock)
		return false
	}

	// channel 满了，发送方会被阻塞。接下来会构造一个 sudog

	// 获取当前 goroutine 的指针
	gp := getg()
	mysg := acquireSudog()
	mysg.releasetime = 0
	if t0 != 0 {
		mysg.releasetime = -1
	}

	mysg.elem = ep
	mysg.waitlink = nil
	mysg.g = gp
	mysg.selectdone = nil
	mysg.c = c
	gp.waiting = mysg
	gp.param = nil

	// 当前 goroutine 进入发送等待队列
	c.sendq.enqueue(mysg)

	// 当前 goroutine 被挂起
	goparkunlock(&c.lock, "chan send", traceEvGoBlockSend, 3)

	// 从这里开始被唤醒了（channel 有机会可以发送了）
	if mysg != gp.waiting {
		throw("G waiting list is corrupted")
	}
	gp.waiting = nil
	if gp.param == nil {
		if c.closed == 0 {
			throw("chansend: spurious wakeup")
		}
		// 被唤醒后，channel 关闭了。坑爹啊，panic
		panic(plainError("send on closed channel"))
	}
	gp.param = nil
	if mysg.releasetime > 0 {
		blockevent(mysg.releasetime-t0, 2)
	}
	// 去掉 mysg 上绑定的 channel
	mysg.c = nil
	releaseSudog(mysg)
	return true
}
```

这段源码里面有一段快速判断向Channel发送数据会失败的情况：
```go
if !block && c.closed == 0 && ((c.dataqsiz == 0 && c.recvq.first == nil) || (c.dataqsiz > 0 && c.qcount == c.dataqsiz)) {
	return false
}
```

上面这个代码的意思是：如果上面这个条件成立，那么一定会发送失败，好让这个函数快速返回。

block表示是否阻塞；它是函数的参数，不会发生改变；
closed表示是否关闭；他可能会被其他的goroutine改变；

最后一项，涉及到三个变量：c.dataqsiz（底层循环数组的大小），c.recvq.first（接收的goroutine队列的第一个），c.qcount。

# 从 channel 接收数据的过程
不会容易多少，基本也是那一系列流程下来。

# 关闭Channel的过程
没有仔细研究代码；
大概的流程是：
nil？
加锁；
hasClosed?
senq和revq两个goroutine的队列；
然后把对应的goroutine连接成一个链表；

# 从一个已经关闭的Channel里面可以读出值来吗？
主要是从Channel读取数据的时候还有一个ok的值，只有当这个值为false的时候才说明已经无值可读。

```go
func TestChanClosed() {
    ch := make(chan int, 5)
    ch <- 5
    ch.Close()

    v1 := <- ch

    v2, ok := <- ch


}
```

# 优雅的关闭Channel
文章总结了集中最主要的情况；
