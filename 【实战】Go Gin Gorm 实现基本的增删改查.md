# 【实战】Go Gin Gorm 实现基本的增删改查
# 导入 gin和 gorm 

实现 go结构体自动迁移到 数据库中
```go
func TestGormConnection(t *testing.T) {
	dsn := "root:risk@tcp(127.0.0.1:3306)/crud-list?charset=utf8mb4&parseTime=True&loc=Local"
	// 获取数据库连接
    db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
    // 获取数据库对象
	sqlDB, err := db.DB()

	sqlDB.SetMaxIdleConns(10)
	sqlDB.SetMaxOpenConns(100)
	sqlDB.SetConnMaxLifetime(10 * time.Second)

	type List struct {
		// 添加主键以及设置软删除
		gorm.Model
		Name    string
		State   string
		Phone   string
		Email   string
		Address string
	}

    // 调用自动迁移的方法
	err = db.AutoMigrate(&List{})
	if err != nil {
		t.Fatalf("出现了错误")
	}
}

```

- 默认需要往你定义的结构体里面添加一个 gorm.Model, 代表这是一个经过gorm管理的数据实体, 它会为里面添加一些规范例如**主键**
给结构体添加 gorm.Model
还有默认这么做的时候, gorm实际在数据库里面添加的表会添加一个复数,  不解释太多, 自己看: 
```go
...
db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{
		NamingStrategy: schema.NamingStrategy{
			SingularTable: true,
		},
	})
```


**结构体的优化**:
gorm使用go语言结构体的tag功能实现对应的约束功能. 

gorm 指定类型
json 表示字段作为json接受的时候对应的json的名字
binding required 表示必须传入, 否则会报错


定义好结构体之后实际上就可以定义对应的 crud的接口了

我不懂前端, 所以我直接就写这里如果服务器发送对应请求的情况: 

