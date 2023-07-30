# 前言
context 是 golang 中的经典工具，主要在异步场景中用于**实现并发协调**以及对 goroutine 的**生命周期控制**. 除此之外，context 还兼有一定的数据存储能力. 本着知其然知其所以然的精神，本文和大家一起深入 context 源码一探究竟，较为细节地对其实现原理进行梳理.

# 核心数据结构
context.Context是一个接口，它关心四个东西：
```go
type Context interface {
    Deadline() (deadline time.Time, ok bool)
    Done() <-chan struct{}
    Err() error
    Value(key any) any
}
```

其中这个Done的含义是：返回一个chan，这个chan的作用应该是判断ctx是否完成。

# emptyCtx
名字的含义是空context，实现了ctx接口：都是nil或者无。

Background和TODO都是空。这两个名字就像变量名一样，表示他们的作用是不一样的。


# cancelCtx
重点是：它一定作为子ctx，并且它包含一个子ctx的set

这个Done是返回一个chan：主要是懒加载的机制。


待更新。有点恶心啊这东西。



或许解决问题的快乐才是我想要的。

