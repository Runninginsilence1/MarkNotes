# Gorm
# Auto Migrate操作
gorm中的Automigrate ()操作，其作用主要是刷新数据库中的表格，使其保持最新，即让数据库之前存储的记录的表格字段和程序中最新使用的表格字段保持一致（只增不减）。

# 快速入门，连接上数据库
使用 go get 导入依赖
```shell
go get -u gorm.io/gorm
go get -u gorm.io/driver/sqlite
```

获取数据库连接（应该）
```go
// 遍历 tcp的资源定位符
    dsn := "root:risk@tcp(127.0.0.1:3306)/crud-list?charset=utf8mb4&parseTime=True&loc=Local"

	db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
	if err != nil {
		panic(err)
	}

```

# go test
test 函数 中不能使用 返回值，因为是直接运行的缘故，所以不能有返回值，类似main函数不能有返回值和参数。

# gorm 添加主键
在结构体里面添加 gorm.Model

# 使用自动迁移的时候
使用自动迁移的时候，不要自动把表名变成复数。

在 Open那个函数里面添加 配置对象：
```go
db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{
		NamingStrategy: schema.NamingStrategy{
			SingularTable: true,
		},
	})
```

# 对象实体和结构体的实体的定义
通过 gorm自己的配置项可以自己配置对应的 表字段的内容。。。
例如：
`gorm:"type:varchar(20); not null" json:"name" binding:"required"`
意思就是 定义它的字段类型，约束，json的名称还有是否必须填写 


# gin的模型绑定和验证
文档: 要将**请求体(Request Body)绑定到结构体**中，使用模型绑定。
ShouldBindJSON

可以直接看文档,,,

# gin从url获得信息的时候
使用Query还是Param?

前者是 类似 ?name=zhangsan&age=14 的这种

后者是没有键值对, 直接传递的值

# 前端部分
使用的是 Vue 和 Vite构建的应该是.
## 搭建 Vue + Vite的模板
使用npm搭建一个模板项目, 具体看vite的文档, 然后按照 npm内部的提示来就行了


## (可选)使用 github管理项目
和我知道的 git的使用差不多

## 引入 vue的一个ui组件: element-ui
使用npm安装

