# Go设计模式
**定义**：设计模式是指在软件开发过程中经过反复验证，被广泛使用的一种**经验性的软件设计经验**，是对软件设计中普遍存在问题的一种**可重用的解决方案**，提供了一种在特定情境下，解决特定问题的成功的经验。
设计模式的目的是为了提高程序设计的复用性、可维护性、灵活性、可扩展性、可读性和可理解性。它们是经过实践检验的编程思想。

设计模式一般是针对面向对象这一设计范式；其他类型的范式也有设计模式，但是一般是指前者。

## 一句话总结
前人栽树后人乘凉。

# 单例模式
这个是最简单，也最常用的一种设计模式。
一般来说，如果一个实例涉及到**资源访问**；或者在一个实例即可解决问题，为了提高性能（不要反复创建和销毁该对象）的情况下，使用单例模式。

## 什么是单例模式？
> 单例模式指仅允许创建一个对象的设计模式。它通常应用于控制对某些资源的访问，例如数据库连接、线程池等等。通过单例模式，可以确保系统中只存在唯一一个实例，并提供一个全局访问点，方便其他对象使用。

上面这句话总结了single instance的三个关键点：
**干什么**：资源访问
**效果**：整个程序的生命周期只能存在一个实例；
**实现**：提供全局访问点来方便其他对象使用，是典型的的**生产者**。

## Go中实现单例模式
根据创建时机的不同，可以分为懒汉和饿汉：

懒汉是只有在使用时才进行加载，是“延迟加载”思想的一种体现
```go
package singleton

import "sync"

var (
	instance *Singleton
	once     sync.Once
)

type Singleton struct {
}

// once.Do中的逻辑只会执行一次，sync包的注释说明
func GetInstance() *Singleton {
	once.Do(func() {
		instance = &Singleton{}
	})

	return instance
}
```

饿汉则是程序启动后立刻加载；
```go
package singleton

var instance *Singleton = &Singleton{}

type Singleton struct {
}

func GetInstance() *Singleton {
	return instance
}
```

另外，在多线程环境下，会通过锁来进行同步控制创建单例的行为；
并且通过双重检查（**加锁前后各进行一次检查**）来减少使用互斥锁的次数。 基于懒汉式。

```go
package singleton

import "sync"

var (
	instance *Singleton
	mu       sync.Mutex
)

type Singleton struct {
}

func GetInstance() *Singleton {
	if instance == nil {
		mu.Lock()
		defer mu.Unlock()
		if instance == nil {
			instance = &Singleton{}
		}
	}
	return instance
}
```

## 实战应用
使用单例模式来实现数据缓存：

```go
package main

import (
	"fmt"
	"sync"
)

type Cache struct {
	store map[string]string
	mu    sync.Mutex
}

var instance *Cache

func GetCacheInstance() *Cache {
	if instance == nil {
		instance = &Cache{
			store: make(map[string]string),
		}
	}
	return instance
}

func (c *Cache) Get(key string) (string, bool) {
	c.mu.Lock()
	defer c.mu.Unlock()

	val, ok := c.store[key]
	return val, ok
}

func (c *Cache) Set(key, val string) {
	c.mu.Lock()
	defer c.mu.Unlock()

	c.store[key] = val
}

func main() {
	cache := GetCacheInstance()

	cache.Set("name", "Tom")
	if val, ok := cache.Get("name"); ok {
		fmt.Println(val)
	}
}
```

是前面理论的实际应用。
值得注意的是这里的操作都**使用到了锁**。


## 一句话总结
怎么样实现全局一个实例的过程。

# 简单工厂模式
简单工厂模式是一种**生成器模式**，它将生成对象的代码封装在一个工厂类中，其职责是创建对象。
个人认为这是将实例化的行为封装成一个类，对比与原始的构造器要更加的便于管理与维护。

> 该模式允许客户端通过**使用与接口或类无关的方式**来创建对象，并**通过传递参数来避免创建其他具体实例**。这种方式非常方便并且易于维护，因为它将所有的实例化过程集中在一个类中。

集中化管理的一种体现。

以下的代码是根据解析格式的不同返回不同实现的Parser的实现代码：
```go
// 定义parser接口
type Parser interface {
    Parse(body []byte) (interface{}, error)
}

// 定义不同的parser类型
type JSONParser struct{}
type XMLParser struct{}

// 实现parser接口
func (p *JSONParser) Parse(body []byte) (interface{}, error) {
    // 解析JSON并返回结果
}

func (p *XMLParser) Parse(body []byte) (interface{}, error) {
    // 解析XML并返回结果
}

// 定义一个factory，用于根据请求类型创建相应的parser
type ParserFactory struct {}

// 工厂方法，返回相应的parser实例
func (f *ParserFactory) CreateParser(requestType string) Parser {
    switch requestType {
    case "json":
        return &JSONParser{}
    case "xml":
        return &XMLParser{}
    default:
        return nil
    }
}
```

只需要**提供需求参数**，工厂就能自动的返回对应的实例；同时所以的**实例化逻辑都集中在一起**，便于维护。

## 一句话总结
主要还是一个封装的应用。

# 工厂模式
上面才写了简单工厂，这里又写工厂，我个人觉得：

简单工厂是强调在实例化的逻辑集中在一起，方便根据参数统一调度以及逻辑的统一维护；

工厂是强调实例化与构造器解耦，实例化通过工厂提供的函数来获取；

两者稍许不同...暂时不管。

工厂模式的代码
```go
package main

import "fmt"

// 定义一个接口，表示待创建的对象
type Product interface {
	Name() string
}

// 定义一个结构体，表示具体的对象
type ProductA struct{}

// 实现产品的方法
func (p *ProductA) Name() string {
	return "ProductA"
}

// 定义一个工厂对象类型
type Factory interface {
	Create() Product
}

// 定义一个工厂结构体来创建ProductA实例
type ProductAFactory struct{}

// 创建ProductA实例
func (f *ProductAFactory) Create() Product {
	return &ProductA{}
}

func main() {
	// 创建ProductA
	paFactory := &ProductAFactory{}
	pa := paFactory.Create()
	fmt.Println(pa.Name())
}
```

# 抽象工厂设计模式
索引的索引，工厂的工厂。 尽管绕口，但是这是广泛运用的设计模式，不得不品尝。

> 抽象工厂是一种**创建其他工厂**的工厂，它可以一起实现**一组相关对象**的创建。这种设计模式属于创建型模式，提供了一种创建家族工厂的方法，而无需指定它们的具体类别。

对工厂进行进一步封装。


> 当需要生产一组有关联的产品时，例如：苹果电脑需要生产苹果显示器、苹果键盘和苹果鼠标，已达到产品之间的协同工作。
>当需要将一组抽象概念实现为一组具有依赖关系的实体组件时。
>当需要遵循开闭原则，而需要添加新功能时，只需扩展原始代码以实现新的工厂和产品。

1. 要生产一组有关联的东西，用抽象工厂进行统一管理；
2. 第二个没怎么看懂；
3. 第三个是实现业务拓展；

也没有很看懂。

[Go抽象工厂模式](https://juejin.cn/post/7212804044023726136)

卡在这里吧！
毕竟属于这一方面的知识。


用聚合函数统计数量
count
然后用group来根据对应的分组
select type, count(type) as num from table_name group by type order by num