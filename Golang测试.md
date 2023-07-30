Go自带一个测试机制，包含测试结果、测试函数输出、覆盖率、性能Benchmark等测试结果。

# 编写一个测试函数
1. 测试函数一般在文件XXXX_test.go中；
2. 测试函数的名字和签名为：`func TestXXXX(t *testing.T) {}`
3. 使用go test运行当前目录下的所有Test

一个最简单的测试包括：
1. 调用被测试函数，并输出结果；
2. 进行断言

demo:
```go
package main

import (
	"testing"
)

func TestAdd(t *testing.T) {
	// Assert your func, assumption:
	//result := Add(2, 3)
	result := 5
	if result != 5 {
		t.Errorf("Add(2, 3) returned %d, expected 5", result)
	}
}

func TestSubtract(t *testing.T) {
	//result := Subtract(5, 3)
	result := 3
	if result != 2 {
		t.Errorf("Subtract(5, 3) returned %d, expected 2", result)
	}
}

```

