# 剑指offer 题解
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
dfs 一般是遍历: 因为有回溯的原因, 代码是很简洁的. 

我直接贴代码了, 如果不懂或者忘记代码怎么写了随时回来看

// 二维数组内是否存在单词的问题
```go
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

