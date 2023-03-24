# 笔记

# GitHub在一个具体的项目里面发挥的作用
1. 代码的日常提交
2. 生产环境发布： 大部分的生产环境的搭建都是基于github的。
3. 开发环境的搭建
4. 测试环境的搭建

如果一个新项目要开始立项，首先你就应该在github上创建链接； ❗ 还没做
然后在本地创建对应的项目文件夹。之后把两个同步起来，一个 Git的基本环境就算搭建起来了。

# Gin的快速入门
Gin是go里面最常见的一个http框架了，很多公司用，轻量级。
Web框架基本都是一个套路： 一般main函数里面就是启动服务器，然后下一层就是对应的接口绑定对应的控制器，和 Spring Boot是一样的。

要启动一个gin的服务器
```go
r := gin.New()

r.POST("/login", func(c *gin.Contest){})

r.Run(":8080")
```

还有go语言的包名是可以设置别名的，等同于**namespace**

创建通用的http服务：

1. 一个基本的http业务的模板代码大概是这个样子，以订单（List）为例：
```go
func List(c *gin.Context) {
    req := ListRequest{}
    c.ShouldBindJSON(&req)

    // Your service here...

    resp := ListResponse{}
    c.JSON(http.StatusOK, &resp)
}
```

2. 每一种业务，都设置一个对应的struct，并根据具体的情况设置它的fields
以一个订单（List）为例：
首先一个请求对应有页面属性（Page），以及一页显示的订单的数量（Limits）

然后一个订单项（ListItem），暂时就考虑Id和Name

3. 然后业务经过一个SQL QUERY 之后，得到一个结果

最后

# git可视化工具sourcetree
它是免费的。 什么时候我可能会去看一下。
不过它是用于git进行上传的，所以暂时不用管也可以。


# 第三天的流程
1. 首先是github，然后是简历项目文件夹； 注意前后端分离，我那个就是指提交了后端，那个肯定不行的
2. 第二步就是导入gin，可以跑一个基本的路由服务，用apipost来测试
3. 然后就是一些常用的api调用：例如any；post；group（中转路由，相当于默认路由；）它们负责将请求转发给对应的handler进行处理；
4. 然后一个通用的handler（Spring里面应该是Controller层次的东西；）的模板代码；
5. 暂时就写这么多


# Docker
## 安排项目的目录
在讲解到这里的时候他给了我的项目结构一点小小的启发：
一个标准的项目的目录结构应该是：
源代码一个目录命名为source，里面分为前端和后端
文档一个目录命名为docs
然后使用到其他的中间件时，现在的主流方案都是使用docker来进行部署，所以会有一个docker的文件夹，里面应该是装的dockerfile之类的东西
然后使用git进行项目管理，所以要记得要添加gitignore文件

## Docker的使用






