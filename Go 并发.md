# 番外：Rob Pike关于Go并发的演讲

有一点能够确定：go在语言层面支持并发特性的，就是三个东西：goroutine、channel还有select。这是一个它的并发模型开发非常简单的证据之一。只不过，我并不是能很好的理解这种开发的思想。我还需要大量的训练。我认为，我不能急躁。我需要一定的时间来训练相关的东西。

从CSP开始、goroutine的基本用法、Channel的基本用法（一个返回Channel的函数，这应该适用于调用方作为纯消费者的时候）（使用装饰器来进行扇入操作）

Rob Pike提到的用法是：
**生成器模式（Pattern）**
如果并发协调由channel来控制，那么就生成它。很简单的使用。
体现在代码中则是：定义好一个channel，然后使用go来启动一个生产者goroutine。
在一个函数中定义好与channel相关的行为，然后将这个chan返回；
其实time.After()这个函数就是这么做的，所以用在select里面可以做超时控制
```go
for {
	select {
		case v := <- c:
			fmt.Println("Hello")
		case timeout:=<-time.After(1 * time.Second):
			fmt.Println("你已经结束了.")
			// 看一下这个是什么类型的值
			fmt.Printf("Type is %T\n", timeout)
			return
	}
}
```

以上是定义好channel。如果有多个类似的channel，并且想让他们在准备就绪的情况下执行他们的行为，在不使用go自带的select的情况下，可以通过fan-in（扇入函数）实现一种类似于IO多路复用的效果：
```go
func fanIn(input1, input2 <-chan string) <-chan string {
	mid := make(chan string)
	go func() {for {mid <- <-input1}}
	go func() {for {mid <- <-input2}}
	return mid
}
```


**select**的用法可以用一句话来总结：如果一个select中用来存放阻塞操作，并当阻塞解除了就运行对应的逻辑。如果整个结构的操作都是阻塞的，那么select就阻塞住了。

那么基于time.After这个函数的超时控制就非常好理解了：
它会返回一个channel，并且在指定的时间后channel就会接受到值。
这样select就能解除阻塞，并且也实现了超时控制。


## Google Search on Go Backend
一个实际的，显示的需求，模拟Google引擎的后端搜索服务
![[Pasted image 20230701163520.png]]




# Goroutine
**Goroutine的三种用法**
```go
//go 关键字放在方法调用前新建一个 goroutine 并执行方法体
go GetThingDone(param1, param2);

//新建一个匿名方法并执行
go func(param1, param2) {
}(val1, val2)
// 这个说法也有点那个

//直接新建一个 goroutine 并在 goroutine 中执行代码块
// 这种好像在 go 1.18中不可用
// go {
//     //do someting...
// }
```

一个 goroutine必定对应着一个函数。
**绑定goroutine的时候，该函数的返回值会被忽略，请使用 channel**

goroutine小细节
> 所有 goroutine 在 main() 函数结束时会一同结束。
goroutine 虽然类似于线程概念，但是从调度性能上没有线程细致，而细致程度取决于 Go 程序的 goroutine 调度器的实现和运行环境。
终止 goroutine 的最好方法就是自然返回 goroutine 对应的函数。虽然可以用 golang.org/x/net/context 包进行 goroutine 生命期深度控制，但这种方法仍然处于内部试验阶段，并不是官方推荐的特性。
截止 Go 1.9 版本，暂时没有标准接口获取 goroutine 的 ID。

有办法可以获取 goroutine id，但是这种做法就像 C++编程一样危险。一般不向外部暴露。



# Channel
## 并发编程的复杂性
并发编程的**难度在于协调**，而协调就要通过交流，从这个角度看来，并发单元间的**通信是最大的问题**。
在工程上，有两种最常见的并发通信模型：共享数据和消息。

锁的使用比较直观，但是写出来的代码臃肿不堪且难以理解。

Go使用另外一种通信模型，消息机制。（顺便一提安卓的原生并发也是使用的一个handler的消息机制）
> 消息机制认为每个并发单元是自包含的、独立的个体，并且都有自己的变量，但在不同并发单元间这些变量不共享。每个并发单元的输入和输出只有一种，那就是消息。这有点类似于进程的概念，每个进程不会被其他进程打扰，它只做好自己的工作就可以了。不同进程间靠消息来通信，它们不会共享内存。


## 
> channel 是进程内的通信方式，因此通过 channel 传递对象的过程和调用函数时的参数传递行为比较一致，比如也可以传递指针等。如果需要跨进程通信，我们建议用分布式系统的方法来解决，比如使用 Socket 或者 HTTP 等通信协议。Go语言对于网络方面也有非常完善的支持。

个人理解：channel的传参和函数有点类似，引用传递的话也是用指针。
并且一个channel对应一个类型（类比 Unix的管道），可以**类型安全**
