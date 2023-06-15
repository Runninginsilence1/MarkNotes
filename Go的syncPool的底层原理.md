# Go的syncPool的底层原理
[来源](https://www.bilibili.com/video/BV1324y117PL/)

pool是go官方提供的一种用于减轻gc压力，提高性能的一种方案；
思路和线程池一致；

pool的特点：
并发安全，可以经过多个goroutine进行调用；

pool的使用：
主要是New（func类型，由程序员自己定义）、Get（从pool中取）和Put（存）三个方法

pool是一个struct，它的结构是：
```go
type Pool struct {
	noCopy noCopy

    // 指针，实际上是指向的[P]poolLocal，p就是gmp模型里面的p，表示实际线程
	local     unsafe.Pointer // local fixed-size per-P pool, actual type is [P]poolLocal
	localSize uintptr        // size of the local array

    // 这个victim是计算机架构的一种技术，作用是降低gc压力和提高缓存命中率
    
	victim     unsafe.Pointer // local from previous cycle
	victimSize uintptr        // size of victims array

	// New optionally specifies a function to generate
	// a value when Get would otherwise return nil.
	// It may not be changed concurrently with calls to Get.
	New func() any
}
```