# GOROOT 和 GOPATH
[博客地址](https://blog.csdn.net/qq_38151401/article/details/105729884)

go中没有项目的说法，只有包, 其中有两个重要的路径，GOROOT 和 GOPATH

GOROOT：GOROOT就是Go的安装目录，（类似于java的JDK）
GOPATH：GOPATH是我们的工作空间,保存go项目代码和第三方依赖包

GOPATH可以设置多个，其中，第一个将会是默认的包目录，使用 go get 下载的包都会在第一个path中的src目录下，使用 go install时，在哪个GOPATH中找到了这个包，就会在哪个GOPATH下的bin目录生成可执行文件

GOROOT 类似jdk, 一般不需要过多了解

GOPATH是开发时的工作目录。用于：

1. 保存编译后的二进制文件。
1. go get和go install命令会下载go代码到GOPATH。
1. import包时的搜索路径

例子: 
假设程序中引入了如下的包：
```go
import "Go-Player/src/chapter17/models"
```

第一步：Go会先去GOROOT的scr目录中查找，很显然它不是标准库的包，没找到。
第二步：继续在GOPATH的src目录去找，准确说是GOPATH/src/Go-Player/src/chapter17/models这个目录。如果该目录不存在，会报错找不到package。在使用GOPATH管理项目时，需要按照GO寻找package的规范来合理地保存和组织Go代码。

简单地说, 你需要把 GOPATH也理解成 go的包的一个, 而不同的项目对于 go而言也就是不同的包.

**在 go 1.11之间使用 gopath构建项目, 但是之后就不再这么做了. 因此前面的你可以当看了个寄**

go env 中的 GO111MODULE 的值决定是否启用 GOMODULE

关于两者的区别: 
GO111MODULE=off，go命令行将不会支持module功能，寻找依赖包的方式将会沿用旧版本那种通过vendor目录或者GOPATH模式来查找。
GO111MODULE=on，go命令行会使用modules，而一点也不会去GOPATH目录下查找。
GO111MODULE=auto，默认值，go命令行将会根据当前目录来决定是否启用module功能。这种情况下可以分为两种情形：
    当前目录在GOPATH/src之外且该目录包含go.mod文件
    当前文件在包含go.mod文件的目录下面。

# GOMODULE
[博客原文](https://blog.csdn.net/sinat_23156865/article/details/100655475)

