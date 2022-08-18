# gorm
做个备忘笔记, 忘记框架的使用方法时可以用来查一下
# 通过数据库驱动连接到 database
以MySQL为例, 其他数据库查看 [连接到数据库](https://gorm.io/zh_CN/docs/connecting_to_the_database.html)
```go
// 导包
import (
  "gorm.io/driver/mysql"
  "gorm.io/gorm"
)

func main() {
  // 参考 https://github.com/go-sql-driver/mysql#dsn-data-source-name 获取详情
  // 定义数据源uri(?)
  // 关于charset和parseTime 不同的版本有一些不同
  dsn := "user:pass@tcp(127.0.0.1:3306)/dbname?charset=utf8mb4&parseTime=True&loc=Local"
  // 使用 open 函数可以测试连接
  db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
}
```

其中 open函数的第二参数接受一个配置参数
MySQL 驱动程序提供了 一些高级配置 可以在初始化过程中使用，例如：
```go
db, err := gorm.Open(mysql.New(mysql.Config{
  DSN: "gorm:gorm@tcp(127.0.0.1:3306)/gorm?charset=utf8&parseTime=True&loc=Local", // DSN data source name
  DefaultStringSize: 256, // string 类型字段的默认长度
  DisableDatetimePrecision: true, // 禁用 datetime 精度，MySQL 5.6 之前的数据库不支持
  DontSupportRenameIndex: true, // 重命名索引时采用删除并新建的方式，MySQL 5.7 之前的数据库和 MariaDB 不支持重命名索引
  DontSupportRenameColumn: true, // 用 `change` 重命名列，MySQL 8 之前的数据库和 MariaDB 不支持重命名列
  SkipInitializeWithVersion: true, // 根据当前 MySQL 版本自动配置
}), &gorm.Config{})
```
关于配置方面不再赘述, 详情去看文档
...
