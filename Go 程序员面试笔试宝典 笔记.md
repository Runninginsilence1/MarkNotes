# Go 程序员面试笔试宝典 笔记
# 数组与切片
## 数组和切片有什么异同
这么久了我看到的最好的一个对于切片的定义: 
> slice 是对数组的封装(抽象)，它描述一个数组的片段。
> 切片要比数组灵活得多，因为长度是数组类型的一部分。

slice.array指向的**底层数组可以被多个切片共同指向**, 所以注意. 

## Slice的扩容机制
引起 Slice扩容的函数是`append`函数。
它的参数可变，也可以通过传入 Slice...的方式直接append一个切片。

调用 append函数返回值是一个新的slice，Go编译器不允许调用了 append 函数后不使用返回值。（这是规定，不过其他函数就不一定了。）

使用append时如果slice发生了扩容，那么：
1.18版本以前：
> 当原 slice 容量小于 1024 的时候，新 slice 容量变成原来的 2 倍；原 slice 容量超过 1024，新 slice 容量变成原来的1.25倍。

1.18版本：
当原slice容量(oldcap)小于256的时候，新slice(newcap)容量为原来的2倍；原slice容量超过256，新slice容量newcap = oldcap+(oldcap+3*256)/4。

通过作者分析源码（我自己看不懂）
```go
// go 1.9.5 src/runtime/slice.go:82
func growslice(et *_type, old slice, cap int) slice {
    // ……
    newcap := old.cap
	doublecap := newcap + newcap
	if cap > doublecap {
		newcap = cap
	} else {
		// ……
	}
	// ……
	
	capmem = roundupsize(uintptr(newcap) * ptrSize)
	newcap = int(capmem / ptrSize)
}
```
顺便解释一下 threshold是临界值的意思。
在超过上面里面提到的大小之后，go会进行内存对齐，所以新的容量总是大于等于 上面规律的大小。

slice发生扩容的时候，调用 growslice函数，里面有一个参数cap（表示如果要完成扩容所需要最小的容量）
函数内部会将 old.cap 的两倍和 这个cap进行对比，取大的那一个。

继续看


# 哈希表
就是字典. 

底层的数据结构一般是 哈希查找表(数组 \+ 链表) 和 搜索树. 

哈希查找表一般会存在“碰撞”, 解决办法 一般就是挂链表和开放地址法(按照某种规律重新选择一个位置存放). 

平衡树(avl树, 红黑树)的原理和实现都比较难...所以...

自平衡搜索树法的最差搜索效率是 O(logN)，而哈希查找表最差是 O(N)。

并且, 前者是有序的, 后者往往是无序的. 

## go的实现

# 草稿区域

> Go 语言采用的是哈希查找表，并且使用链表解决哈希冲突。 