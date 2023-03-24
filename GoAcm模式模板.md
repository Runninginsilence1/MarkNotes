# 来源
来自博客[用 Golong 处理 ACM 模式下的输入输出](https://blog.csdn.net/weixin_44211968/article/details/124632136)

# fmt包用来读取数字
## 每行的数字固定
### 不知道行数
因为是固定的数字，所以用于接受输入的参数的数量也是固定的：
Scanln返回两个值（第二个应该是err），然后n是第一个值，总之这个值为0的时候就结束（不知道是登记几个）
因此循环的类型为无限循环加判断n为0时break
```go
func main() {
	a := 0
	b := 0
	for {
		n, _ := fmt.Scanln(&a, &b)  //也可以用 fmt.Scan
		if n == 0 {
			break
		} else {
			fmt.Printf("%d\n", a+b)
		}
	}
}
```

### 知道行数
同上，但是循环更换为fori，次数为0即可；

## 每行的数字不固定，但是知道数量
道理几乎是一样的。 不知道行数的话总是有一个结束标记或者系统的结束标记的，用一个条件语句判断是否结束循环。
然后fmt.Scanln()这个读取的函数换成Scan这个函数；
看代码：
```go
package main

import (
	"fmt"
)

func main() {
	var t int
	for {
        var sum int
        
		fmt.Scan(&t)
		if t == 0 {
			break
		} else {
			a := make([]int, t)
			for i := 0; i < t; i++ {
				fmt.Scan(&a[i])
			}
			for i := 0; i < t; i++ {
				sum += a[i]
			}
		}
		fmt.Println(sum)
	}
}

```

## 不知道数量，知道行数
几乎同理。 我先把其他的代码看完：
```go
package main

import (
	"fmt"
)

func main() {
	var t int
	fmt.Scan(&t)
	for i := 0; i < t; i++ {
		var num, sum, a int
		fmt.Scan(&num)
		for i := 0; i < num; i++ {
			fmt.Scan(&a)
			sum += a
		}
		fmt.Println(sum)
	}
}

```

# 需要整行读取的情况
这个时候需要用到bufio这个包
类似Java，需要用一个Scanner实例来操作；
for里面可以调用Scan方法
Text()方法就是读取到的文本

然后还有用于集合转换的函数：strconv这个包下面的轮子


```go
package main

import (
    "bufio"
    "fmt"
    "os"
    "strconv"
    "strings"
)

func main() {
    inputs := bufio.NewScanner(os.Stdin)
    for inputs.Scan() {  //每次读入一行
        data := strings.Split(inputs.Text(), " ")  //通过空格将他们分割，并存入一个字符串切片
        var sum int
        for i := range data {
            val, _ := strconv.Atoi(data[i])   //将字符串转换为int
            sum += val
        }
        fmt.Println(sum)
    }
}

```
