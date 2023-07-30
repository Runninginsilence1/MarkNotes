# 整体框架
## 一张图明白冲浪是怎么回事
![[Pasted image 20230702163827.png]]

HTTP就是负责client和server进行交互的协议。

## 启动HTTP服务
不管什么web框架，启动服务器总是最后一步；php和java都没有自带服务器，流行的是tomcat, nginx之类的；

而go的standard lib中包含一个小型的服务器（？不过对我来说肯定是够了）

启动一个基本的web服务器是非常简单的：
```go
// nvim main.go
package main

import (
	"net/http"
)

func main() {
	http.HandleFunc("/ping", func(w http.ResponseWriter, r *http.Request) {
		w.Write([]byte("pong"))
	})

	http.ListenAndServe(":8091", nil)
}
```

首先是我粗略的理解：
对于服务器来说，它获取客户端的请求路径，然后调用一个handler去解析。这就是一个最基本的服务器。

所以上面的代码做的其实也就是这件事：
为请求路径配置解析器：http.HandleFunc这个方法接受路径参数string，以及对应的解析逻辑func

注册完毕全部的逻辑之后，然后就启动监听的服务器即可。

但是即使他做的事情很简单，他背后的逻辑也是复杂的。
后面会具体谈到。

## 发送HTTP请求
这个通常发生在浏览器中的行为，要在go中实现也是非常容易的，下面给出一例发送 JSON POST 请求的代码示例.
```go
func main() {
    reqBody, _ := json.Marshal(map[string]string{"key1": "val1", "key2": "val2"})
    resp, _ := http.Post(":8091", "application/json", bytes.NewReader(reqBody))
    defer resp.Body.Close()
    respBody, _ := io.ReadAll(resp.Body)
    fmt.Printf("resp: %s", respBody)
}
```

以上的代码像指定服务器发送了一个数据为json的数据，然后接受到一个res相应（这个在go里面有对应的封装）

## 源码位置一览
![[Pasted image 20230702165254.png]]

# 服务端
## 核心数据结构
服务端涉及到各种各样的数据结构。
### Server
所有服务器端的核心内容都是封装在Server中的。
```go
type Server struct {
    // server 的地址, 例如localhost:8080之类的
    Addr string
    // 路由处理器.
    Handler Handler // handler to invoke, http.DefaultServeMux if nil
    // ...
}
```

**最核心的就是这个Handler**，是他负责请求路径path到达具体的Controller的注册和映射。
其实Spring Boot也是一样的，你知道怎么使用Controller，那其实就可以写网站了。

### Handler
是一个接口，负责对api接口进行处理。只有一个方法：ServeHTTP
```go
type Handler interface {
    ServeHTTP(ResponseWriter, *Request)
}
```

也就是说，只要能够处理一个Request请求，并返回相应的相应，那它就可以是一个Handler。

### ServeMux
可以说Path到handler的路由对象，用一个map来维护。
```go
type ServeMux struct {
	mu sync.RWMutex // 读写锁，所以他是并发安全的
	m map[string]muxEntry // 维护一个字典
	es []muxEntry // slice of entries sorted from longest to shortest. 有序
	hosts bool // whether any patterns contain hostnames. ZZK: A status, but not clear for what.
}
```

## 注册handler
### 本质是存放func到一个map中
路由的工作就是将Path，根据匹配规则（俗称Pattern）匹配到对应的handler上去。**俗称注册**
![[Pasted image 20230702175336.png]]

而注册的过程，就是上面这个从HandleFunc开始的过程。

在 net/http 包下声明了一个单例 ServeMux，当用户直接通过公开方法 http.HandleFunc 注册 handler 时，则会将其注册到 DefaultServeMux 当中.

这个Default也很值得玩味。也许gin中的group就跟他有关。

### 细节：v进行二次封装，模糊匹配的有序排列

```go
// Define function type
type HandlerFunc func(ResponseWriter, *Request)


// ServeHTTP calls f(w, r). Impl interface
func (f HandlerFunc) ServeHTTP(w ResponseWriter, r *Request) {
    f(w, r)
}

// put K:Pattern V:Handler into map
func (mux *ServeMux) HandleFunc(pattern string, handler func(ResponseWriter, *Request)) {
    // ...
    mux.Handle(pattern, HandlerFunc(handler))
}
```
这个注册的过程中有两个逻辑特别重要：
1. 会把pattern和handler封装成muxEntry，然后封装进去；
2. 会特别判断pattern的首位和末位是不是斜杠，因为ServeMux不是有一个有序的slice吗，里面是存储的有序的。

这里具体解释一下appendSorted函数。
这个函数是把一个新的entryMux放到一个 slice entryMux中，并且是按照Pattern的长度有序插入。
实现的方法就是通过Search方法找到要插入的位置；
然后再去做顺序表的指定位置插入：判断索引是否为len；或者引入新数组来完成插入。
slice entryMux中应该都是以斜杠结尾的Pattern，用来做模糊匹配。

总之这样就注册完毕了。

## Server的启动逻辑
![[Pasted image 20230703191757.png]]

这个就是启动一个Server的过程图（我真的是非常的喜欢这个图，画的又好又清晰，就是所谓的手绘风吧）

通常会直接调用http.ListenAndServe这个函数，它会创建一个server的实例，然后再去调用它的方法。里面分别是Listen监听，Serve、然后最底层的Accept这个函数监听网络请求；拿到之后会被抽象成netConn这个实例，然后再一系列操作，得到server.ServerHTTP，它就跟之前ServerMux产生关系了。

经过Handler就可以生成响应Response。


调用 net/http 包下的公开方法 ListenAndServe，可以实现对服务端的一键启动. 内部会声明一个新的 Server 对象，嵌套执行 Server.ListenAndServe 方法.

Listen方法似乎是得到一个监听器，我需要验证我的推测
todo： net.Listen()函数

暂时先将其理解成是监听网络请求的；

核心的是下面的一个方法：Server.Serve这个方法。
体现了 http 服务端的运行架构：for + listener.accept 模式.
就是主loop（无限循环）+ accept监听 + goroutine进行异步处理。

我好像以前在Java中就接触过这个模式了：

```java
while (true) {
	// 假设是这个函数：
	InetAddr conn = InetAddr.listen(port);
	new Thread().run()....

	反正大概就是一个这个过程，跟go是一样的
}
```

总之，
- 将 server 封装成一组 kv 对，添加到 context 当中   
- 开启 for 循环，每轮循环调用 Listener.Accept 方法阻塞等待新连接到达
- 每有一个连接到达，创建一个 goroutine 异步执行 conn.serve 方法负责处理

一个持续运行的命令行程序基本不都是这样做的吗？你像redis的单线程的Server端，其实也是这样来接收来自客户端的连接的。

逻辑还要再往conn.serve里面挖：总之它是用了go关键字异步启动的。这里可能还有东西我不知道。

顺便这里我还知道了context中WithValue的含义：其实就是携带一个kv绑定到context中去；
不过context中kv的实现原理决定了它只能存储少量的数据。

### 关于模糊匹配
模糊匹配是指handler在匹配不到完全一致的路径的时候，启动的机制。
在匹配的时候，go中是这样的：先判断是否完全匹配，如果不匹配，就使用string.HasPrefix来匹配模式串前缀最长的那个一个。
```go
func (mux *ServeMux) match(path string) (h Handler, pattern string) {
	// Directly match
    v, ok := mux.m[path]
    if ok {
        return v.h, v.pattern
    }

    // ServeMux.es 本身是按照 pattern 的长度由大到小排列的
    for _, e := range mux.es {
        if strings.HasPrefix(path, e.pattern) {
            return e.h, e.pattern
        }
    }
    return nil, ""
}
```


# 客户端
## 整体抽象Client
因为是模拟客户端，所以Client中会有这样几个重要的字段：
Transport: 负责HTTP通信
Jar：cookie的管理
Timeout： 超时的设置

## 负责通信的Transport
它是复杂网络通信的字段，是一个接口类型RoundTrip
它负责接收Request，然后返回Response。
```go
type RoundTripper interface {
    RoundTrip(*Request) (*Response, error)
}
```

Transport正是它的一个实现类。


...

介绍了一些其他的字段：包括
Request, Response等。

## Client发起HTTP请求
过程是很容易理清楚的：
初始化请求参数；
获取TCP连接
发送参数
获取结果

![[Pasted image 20230706150820.png]]

和http.HandlerFunc一样，调用公开的Post方法实际上是调用client对象的Post方法。






# Gin
Gin的性能是公认的不错，并且它的底层就是基于go标准库的封装。
