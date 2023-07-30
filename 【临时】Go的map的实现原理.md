# 总结
这个我了解的太浅了，根本不能作为面试的材料。
# 【面试】Go的map的实现原理
它的重要性，就相当于redis这个中间件在面试中的重要性一样。

核心是哈希读写函数的选择，以及哈希冲突的解决；

# 哈希冲突的解决
## 开放寻址法
就是冲突了就去寻找下一个为空的地方，这样实现原理的**底层结构是数组**。
这种方法，哈希表的性能与整个数组的**容量大小**（Cap）的比值有关。 这个值一般被称为装载因子：比值越大，性能越低，在超过70的时候剧烈下降；100时完全失效。

## 拉链法（主流的实现方法）
算法要稍微复杂一点，但是平均查找的长度也比较短，各个用于存储节点的内存都是动态申请的，可以节省比较多的存储空间。

一般会使用数组加上链表，不过一些编程语言会在拉链法的哈希中引入红黑树以优化性能，例如Java。

大致思路就是：如果可以，就直接对应上，如果冲突，就在对应“桶”上新增一个结点。

# Go的map的实现
默认的map并发不安全
```go
type hmap struct {
// 元素的数量，比如只存储一个元素那这个值就是1
	count     int
	flags     uint8
    // 这个数是对数，表示桶的数量，因为桶的数量都是2的倍数
	B         uint8
	noverflow uint16
	hash0     uint32

	buckets    unsafe.Pointer
	oldbuckets unsafe.Pointer
	nevacuate  uintptr

	extra *mapextra
}

type mapextra struct {
	overflow    *[]*bmap
	oldoverflow *[]*bmap
	nextOverflow *bmap
}
```

四个重要的参数： 元素、桶、种子、oldBuckets