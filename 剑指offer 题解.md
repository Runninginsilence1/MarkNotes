# 剑指offer 题解
# 二分查找
二分查找是一种时间复杂度为 O(logn)级别的算法，前提条件是**数组有序**
我们常规的二分查找，是寻找某个元素 target 的下标（并且该数组没有**重复**的元素）。

并且二分查找的题目还有**四种类型的变体。**我会按照提示，总结对应的**模板**

## 四种常规二分查找的模板
1. 第一个大于 target元素的下标；
1. 最后一个大于 target元素的下标；
1. 寻找最后一个小于 target元素的下标；
1. 返回第一个等于的，如果没有，就返回最后一个小于的；


第一个： 
```go
func upper(nums []int, target int) int {
	// 首先是左右边界的初始化（注意是闭区间），一般是下面的代码，
	// todo: 要记得考虑特殊情况噢
	l, r := 0, len(nums)-1
	// Special condition: target greater than right index(On upper function);
	if target > nums[r] {
		return -1
	}
	// In rust, you should use "while" to loop a condition loop.

	// l < r 可以保证你圈定的范围里面至少有一个element
	// 同时， l或r的变化单位每次是1的时候，可以保证退出循环是 l == r is true;
	for l < r {
		// 计算中位，下面的这个计算方式在少数情况下可以防止溢出，推荐这么写，防止有傻逼给你留坑;
		// 硬要写成 (l + r) / 2 也不是不行
		mid := (r-l)/2 + l
		if target < nums[mid] { // Now range is [l, mid]
			r = mid

		} else if target == nums[mid] { // 这里的情况看你二分查找的需求： 比如你这里就是要第一个， 那么已经确认的这个很有可能就是第一个，所以你不能让他溜出你的范围，所以：
			//l = mid
			l = mid + 1
		} else {
			l = mid + 1
		}

	}

	// 判断一下此时 l and r 的状态：
	// 这里就是 l == r，然后又处于第一个的需求，所以优先输出l的位置
	return -1
}

```

另外有一个有意思的： rust自带一个 binary_search的方法，可以完美解决本题：
```rust
impl Solution {
    pub fn search_insert(nums: Vec<i32>, target: i32) -> i32 {
        nums.binary_search(&target).unwrap_or_else(|x| x) as i32
    }
}

作者：jedske
链接：https://leetcode.cn/problems/search-insert-position/solution/rust_jedsek-by-jedske-v5ww/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```


# 树的子结构
提到了一个, 一般二叉树的题目 95%都会使用 递归的方式来解决.
整个的递归的思路就是: 从根结点开始 判断是不是子节点, 是返回, 不是分别判断左子树和右子树.
子树的判断标准是: 先判断当前结点是不是是不是相等, 如果相等, 再利用递归继续去进行判断.

```go
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func isSubStructure(A *TreeNode, B *TreeNode) bool {
    // use recursion to judge answer
    // special answer
    if A == nil || B == nil{
        return false
    }

    // Is B the structure of the A tree?
    // is, return true
    if isSubTree(A, B) {
        return true
    // else judge A.Left && A.Right by func isSubStructer()
    }
    if isSubStructure(A.Left, B) || isSubStructure(A.Right, B) {
        return true
    }

    return false
}

func isSubTree(A, B *TreeNode) bool{
    // if B is nil, B tree has finished ranging.
    if B == nil {
        return true
    }
    // if A is nil but B is not nil, return false
    if A == nil {
        return false
    }

    // Now judge the value equals of the A and B tree
    if A.Val != B.Val {
        return false
    }

    return isSubTree(A.Left, B.Left) && isSubTree(A.Right, B.Right)
}
```

# 数组的重复元素
提到了一个, 如果有专门提到数组是有范围的话, 注意这道题目是不是可以使用原地算法来解决, 思路是使得索引i和对应的值nums[i] 相等, 然后根据题目的要求去添加逻辑.
这个题目也可以用排序和哈希表做, 不过这样不是最优解

```go
// Way 1: sort and traverse array, then compare nums[i] and num[i+1]
// Way 2: use hashmap to record element, return duplicated element
// Way 3 best solution: use in-place and the condition of range of 0~n, compare and return nums[i] == nums[nums[i]] ? nums[i] : continue
func findRepeatNumber(nums []int) int {
    // use way 3
    for i := 0; i < len(nums); i++{
        // tell more details about the loop:
        // Every time you switch nums[i] and nums[nums[i]], the latter's index and value will have equality, but the former still may not got equal, so you need use a loop to keep it right.
        for i != nums[i] {
            if nums[i] == nums[nums[i]] {
                return nums[i]
            }
            nums[i], nums[nums[i]] = nums[nums[i]], nums[i]
        }
    }
    return -1
}
```

# 二维数组中的查找
关于二维数组的一点小技巧
首先是我死记不住的: matrix[row][col], 先行后列, rowLen = len(martix), colLen = len(matrix[0])
row 是行, column 是列.

这个题目的有序条件就是从左下角开始, 转换成代码的初始化就是 row = rowLen - 1, col = 0
```go
// best solution: use the condition of matrix is sorted and matrix[0] is sorted
func findNumberIn2DArray(matrix [][]int, target int) bool {
    if matrix == nil || len(matrix) == 0 || len(matrix[0]) == 0 {
        return false
    }

    // defination of basic args
    colNum := len(matrix[0])
    rowNum := len(matrix)

    // init index: from the left bottom of matrix
    col, row := 0, rowNum - 1

    for col < colNum && row >= 0 {
        if matrix[row][col] > target {
            row--
        } else if matrix[row][col] < target{
            col++
        } else {
            return true
        }
    }
    return false
}
```

# 二维数组的路径以及机器人走格子问题
这两个都是二维数组的深度遍历的问题. 说实话我从来就没自己写出过dfs的代码出来过. 我现在只对我学到的这些东西写一个总结: 
dfs 一般是遍历: 因为有回溯的原因（所以要递归）, 代码是很简洁的. 

递归的dfs的套路：
你的dfs函数做这两件事：
1. 每次检查 一个matrix的格子
1. 递归调用自己去查其他的格子


// 二维数组内是否存在单词的问题
```go
// 长宽和单词的长度
// 哦等等我设置成全局变量了？
var n, m, length int

func exist(board [][]byte, word string) bool {
	n = len(board)
	m = len(board[0])
	length = len(word)

	for i := 0; i < n; i++ {
		for j := 0; j < m; j++ {
			if dfs(board, &word, i, j, 0) {
				return true
			}
		}
	}
	return false
}

func dfs(board [][]byte, word *string, i, j, k int) bool {
	if i < 0 || i >= n || j < 0 || j >= m ||
		// not matching
		board[i][j] != (*word)[k] {
		return false
	}
	// word has traversing done
	if k == length-1 {
		return true
	}

	board[i][j] = '\n'

	res := dfs(board, word, i, j+1, k+1) ||
		dfs(board, word, i+1, j, k+1) ||
		dfs(board, word, i, j-1, k+1) ||
		dfs(board, word, i-1, j, k+1)
	board[i][j] = (*word)[k]
	return res
}
```
机器人走格子问题, 并且里面有个条件, 根据k的特性只需要走完右方向和下方向即可

```go
var row, col, s int
var visited [][]bool

func movingCount(m int, n int, k int) int {
	row, col, s = m, n, k
	visited = make([][]bool, m)
	// if use make() to init matrix, you probably need for loop to use... ah, shit!
	for i := 0; i < m; i++ {
		visited[i] = make([]bool, n)
	}
	return dfs(0, 0)
}

func dfs(i int, j int) int {
	if i < 0 || i >= row || j < 0 || j >= col || visited[i][j] || s < sum(i)+sum(j) {
		return 0
	}
	visited[i][j] = true

	return 1 +
		dfs(i+1, j) + dfs(i, j+1)
}

func sum(n int) int {
	var tmp int
	for n != 0 {
		tmp += n % 10
		n /= 10
	}
	return tmp
}

```

# 剪绳子
这是一个著名的数论的问题, 这里就不考虑数学推导了.
leetcode `小朋友` 的题解
> 数论

> 任何大于1的数都可由2和3相加组成（根据奇偶证明）
> 
> 因为2×2=1×4，2×3>1×5, 所以将数字拆成2和3，能得到的积最大
> 因为2×2×2 \< 3×3, 所以3越多积越大 时间复杂度O(n÷3)，用幂函数可以达到O(log(n/3)), 因为n不大，所以提升意义不大，我就没用。 空间复杂度常数复杂度O(1)
> 双百解法，简单的数论题，DP并不是最优解，套用DP谁都会，而数论会让面试官眼前一亮(尤其如果面试官水平一般的话，只认为DP才能解，数论时间复杂度空间复杂度都远胜DP，他就会怀疑正确性，你即使告诉他怎么证明的，他也会拉着你的代码逐行带入计算，最后被震惊的话，恭喜你幸运的拿到一个SH)。

记住结论: 尽可能让多的 3 相乘, 这样可以让乘积尽可能的大.

引申出一个大数取模的问题: 

> 取模运算公式
> (a + b) % p = (a % p + b % p) % p
> 
> (a × b) % p:
> 如果 a × b < p, (a × b) % p = (a % p × b % p) % p:
> 大于的话就不一定了. 


# 数字的二进制中"1"的个数
理论上来说, 题目如果提到这个那么一般就是要用 位运算和 二进制的相关运算来求解. 
可以讨巧的思路我却一点都想不到: 
一: 用除以二的方式, 这种是讨了题目容易的巧合, 如果题目难一点这种对我就没有意义. 

**位运算的公式**:
二: n 不断地 与n-1相与, 可以把n二进制最右边的第一位1变成0.
这样做, 使用一个count, 到 n = 0的时候, 就得出1的数量了.

# 数值的整数次幂
不要考虑直接循环n次, 别人都懒得骂你傻逼. 
肯定有专门的思路的. 

幂指数可以换成对应的二进制数, 每次

# 二叉树的镜像
这种一定要想到递归, 递归是你基本的思路, 如果有那个本事再考虑使用自定义的栈来实现非递归的算法.

递归的思路: 
递归函数的作用: 使其左右的子树对调, 实现镜像.
并且递归左右子树, 需要在函数自己的逻辑执行之前递归子树.

结束条件: 如果对应的结点已经递归到叶子结点处, 停止递归.

```go
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func mirrorTree(root *TreeNode) *TreeNode {
	if root == nil {
		return root
	}
	
	root.Left = mirrorTree(root.Left)
	root.Right = mirrorTree(root.Right)
	
    root.Left, root.Right = root.Right, root.Left
	return root
}
```

# 二叉树的序列化与反序列化

如果里面**没有重复结点**的话就可以通过 重建二叉树那个题目的方式存储两个数组作为序列化的结果了的

采用层序遍历的方式, 通过存储 null 来判断结点是否有子节点

反序列化的过程大概是: 
得到序列化后的数组, 
使用一个队列存储出队过程中的结果, 
队列的初始化存入

在不停的出队的循环中: 
1. 结点出队
1. 构建左右结点, 如果 curNode的孩子结点不为null, 就放入队列, i++ 和 i++
1. 重复以上过程, 直到队列为空

# 字符串的排列
全排列的问题, 虽然他说是**一个n叉树的问题**, 不过我是一点头绪都没有啊...

# 摩尔投票法
**最优解**是: 摩尔投票法
用的好像是一种**抵消**的思想

```go
// 摩尔投票法
func majorityElement(nums []int) int {
    x, sum := nums[0], 0
    for i := 0; i < len(nums); i++ {
        if sum == 0 {
            x = nums[i]
            sum += 1
        } else {
            if x == nums[i] {
                sum++
            } else {
                sum--
            }
        }
    }

    return x
}
```

# 最小的前k个数
做了这个题目最大的收获竟然是 写笔记对完成代码很有帮助!

因为是k个数, 想到快排的 partition过程 正好是根据 pivot 把数组分为两组的过程, 如果左区间(小于 pivot的数字) 的区间大小正好等于 k, 那么他就符合题目的条件了. 

```go
func getLeastNumbers(arr []int, k int) []int {
	if arr == nil || len(arr) <= 0 || k == 0 {
		return []int{}
	}
	var partition func([]int, int, int) int
	// 定义 partition 函数
	partition = func(nums []int, left int, right int) (bound int) {
		// pivot will be right
		// May wrong: 使用了 right作为基准值, 可能有地方出错了
		pivot := nums[left]

		// 双指针内部开始进行分割
		i, j := left+1, right

		for i < j {
			for i <= j && nums[i] <= pivot {
				i++
			}
			for i <= j && nums[j] >= pivot {
				j--
			}
			if i >= j {
				break
			}

			// 下面进行 partition的过程
			nums[i], nums[j] = nums[j], nums[i]
		}

		// 循环结束后, 需要调整pivot到正确的位置
		// 应该是我这里的调整写错了
		nums[left] = nums[j]
		nums[j] = pivot
		// 这么写其实代码可读性会烂那么一点, 不过我可以试试
		bound = j
		return
	}
	// partition 函数定义结束
	// 定义快排 函数
	var quickSort func([]int, int, int, int) []int
	quickSort = func(nums []int, left int, right, k int) []int {
		// 个人想法哈哈
		bound := partition(nums, left, right)
		if bound+1 == k {
			return nums[0:k]
		}
		if bound+1 > k {
			return quickSort(nums, left, bound-1, k)
		} else {
			return quickSort(nums, bound+1, right, k)
		}
	}
	// 快排 函数定义结束
	return quickSort(arr, 0, len(arr)-1, k)
}

```


# 连续子数组的最大和
一个经典的动态规划的案例, 我也知道很多. 
**最值问题**一般就是用DP来求解. 
动态规划的解题一般三步走: 
1. 知道dp数组的含义, 知道每次回溯的时候哪些是没有必要重复计算的重要数据;
1. 找出 dp之间的关系式, 很类似数学归纳法的那个步骤;
1. 知道初始值怎么求;

这个题目的思路很简单, 直接贴代码了
```go
func maxSubArray(nums []int) int {
	if len(nums) == 1 {
		return nums[0]
	}
	var max func(int, int) int
	max = func(i int, i2 int) int {
		if i > i2 {
			return i
		}
		return i2
	}
	// Shit, I forget the true meaning of this problem
	maxNum := nums[0]
	//dp := make([]int, len(nums))
	dp := nums[0]
	// 关系式: dp[i] = max(nums[i], dp[i-1] + nums[i])

	for i := 1; i < len(nums); i++ {
		dp = max(nums[i], nums[i] + dp)
		maxNum = max(maxNum, dp)
	}

	return maxNum
}

```

# 草稿区域
## 这个动态规划的整理的不错...
动态规划容易出现的问题: 
- 初始值找不对

**动态规划的优化**
动态规划的题目可以很难, 这里总结的是 一般的二维dp数组优化成 一维的情况: 
就是 如果dp\[i]\[j]的关系式: 
```go
dp[i][j] = dp[i-1][j] + dp[i][j-1]
// 或者
dp[i][j] = dp[i-1][j] + dp[i][j-1] + dp[i-1][j-1]
```
那么**分别**可以优化成 
- dp\[i] = dp\[i-1] + dp\[i]
- dp\[i] = dp\[i-1] + dp\[i] + pre

