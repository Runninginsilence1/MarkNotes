# 【临时】Go语言的LeetCode刷题路
# 别人的一些刷题经验的分享
1. 在刷 LeetCode 之前，建议先学习一下基础的 「数据结构」 和 「算法」 知识，这样在开始刷题的时候才不会那么痛苦。
    基础的「数据结构」和「算法」知识包括：

    - 常考的数据结构：数组、字符串、链表、树（如二叉树） 等。
    - 常考的算法：枚举算法、递归算法、分治算法、回溯算法、贪心算法、动态规划 等。


# 数组
只出现一次的数字: 异或运算(双复杂度最优) 或者 set(要求一个set的空间)


# 001 和为某个值的两个数的下标
思路: 我不理解... 这个我真的想不到. hashmap的效率更高, 因此存到hashmap里面再去暴力求解... 哈哈你麻麻地

```go
func twoSum(nums []int, target int) []int {
	// 给map分配空间
	m := make(map[int]int)
		// 遍历数组
	for i := 0; i < len(nums); i++ {
		// 得到另一个数的值, 如果能在map里面找到, 就返回
		another := target - nums[i]
		if _, ok := m[another]; ok {
			return []int{m[another], i}
		}
		m[nums[i]] = i
	}
	return nil
}
```
# 002 用链表 来个位的表示一个数, 并要求return也是这种数据格式
别人的解法里没写太多东西, 感觉我好笨
核心是还是虚拟头结点和 进位问题
```go
func addTwoNumbers(l1 *ListNode, l2 *ListNode) *ListNode {
	head := &ListNode{Val: 0}
	n1, n2, carry, current := 0, 0, 0, head
	for l1 != nil || l2 != nil || carry != 0 {
		if l1 == nil {
			n1 = 0
		} else {
			n1 = l1.Val
			l1 = l1.Next
		}
		if l2 == nil {
			n2 = 0
		} else {
			n2 = l2.Val
			l2 = l2.Next
		}
		current.Next = &ListNode{Val: (n1 + n2 + carry) % 10}
		current = current.Next
		carry = (n1 + n2 + carry) / 10
	}
	return head.Next
}
```

代码的话我大概看懂了, 但是写法的话我是真的想不到
1. 同时遍历链表, 遇到nil 就 0
1. mod的值 就是下一个结点的value
1. carry用来存储 进位的值, 用于下一次循环的计算
1. 最后返回

# 003 最长不重复子串
第一次见到的思路, 和第一题很类似, 用map来判断重复.
虽然我有思路, 可是一要我去写代码就...

题解提供的是滑动窗口的解法.
烦人!
```go
// 解法一 位图
func lengthOfLongestSubstring(s string) int {
	if len(s) == 0 {
		return 0
	}
	var bitSet [256]bool
	result, left, right := 0, 0, 0
	for left < len(s) {
		// 右侧字符对应的 bitSet 被标记 true，说明此字符在 X 位置重复，需要左侧向前移动，直到将 X 标记为 false
		if bitSet[s[right]] {
			bitSet[s[left]] = false
			left++
		} else {
			bitSet[s[right]] = true
			right++
		}
		if result < right-left {
			result = right - left
		}
		if left+result >= len(s) || right >= len(s) {
			break
		}
	}
	return result
}

// 解法二 滑动窗口
func lengthOfLongestSubstring1(s string) int {
	if len(s) == 0 {
		return 0
	}
	var freq [127]int
	result, left, right := 0, 0, -1

	for left < len(s) {
		if right+1 < len(s) && freq[s[right+1]] == 0 {
			freq[s[right+1]]++
			right++

		} else {
			freq[s[left]]--
			left++
		}
		result = max(result, right-left+1)
	}
	return result
}

// 解法三 滑动窗口-哈希桶
func lengthOfLongestSubstring2(s string) int {
	right, left, res := 0, 0, 0
	indexes := make(map[byte]int, len(s))
	for left < len(s) {
		if idx, ok := indexes[s[left]]; ok && idx >= right {
			right = idx + 1
		}
		indexes[s[left]] = left
		left++
		res = max(res, left-right)
	}
	return res
}

func max(a int, b int) int {
	if a > b {
		return a
	}
	return b
}
```


# KMP 算法个人理解
首先 KMP 算法可以把时间复杂度降低至 O(m*n) 的原因是 主串的指针不必再回溯. 
可以这么做的理由是 在已经匹配的部分里, 

```java
// next数组求法
void getNext (char[] ch, int length, int[] next) {
    next[1] = 0;
    int i = 1, j = 0;
    while (i <= length) {
        if (j == 0 || ch[i] == ch[j]) {
            next[++i] = ++j
        } else {
            j = next[j]
        }
    }
}
```