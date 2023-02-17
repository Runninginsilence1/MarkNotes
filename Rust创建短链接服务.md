# Rust创建短链接服务
我在看一个从零开始的rust的项目，他能让我体验一遍rust程序开发的流程。

好吧，我在尝试。
首先官网上提供的脚本，不对，应该说命令可以正常的在我腾讯云的服务器的终端里面运行。

首先它使用了一个web框架。名字不用管，然后再进行cargo run的时候，会因为连接不到外网而导致下载不到依赖失败。
我在百度上搜到了一个办法，就是修改镜像源为国内的。
然后视频里也提供了一个办法，也是修改镜像语言为国内的，我用的是第1种，如果有机会的话就看一下第2种。

另外如果希望这个配置的级别是项目而非整个rust应用的话，可以在项目文件里面新建cargo文件夹。就是层次变了。

用curl测试一下。没有问题，挺好的。

下面这些部分我可能就要详细来写了。
正式开始开发这个短链接服务，首先是API。

似乎是提供了三个API。

他这里添了三行步骤吧，应该是。
第1步，根据URL生成短链接代码
第2步根据生成的短链接代码跳转至原来的url
第3步，查询全部生成的短链接地址。

在那个web框架的原始页面。

首先引入依赖。
```toml
serde = { version = "1.0.145", features = ["derive"] }
serde_json = { version = "1.0.2", optional = true }
nanoid = "0.4.0"
```
其中1，2个是用来进行序列化的。
第3个是用来生成短链接代码的。

现在开始编写和API有关的代码。
首先需要在src目录下创建一个名字叫API的文件夹。

然后在主函数那个文件里面，把API添加进去。

然后在api文件夹中创建links.rs文件，作为代码文件，并在mod.rs里面设置它为可导出；
```rust
pub mod links;
```

然后，在links里面先添加代码：
```rust
// 引入序列化工具
use serde::{Deserialize, Serialize}

// 创建Link结构体
#[derive(Deserialize, Serialize, Debug, Clone)]
struct Link {
    tiny_code: String, // 应该是短链接代码
    origin_url: String, // 原始的url地址

}

// 这里应该是用另外的一个结构体来进行添加短链接的操作
#[derive(Deserialize,  Clone)]
struct ApiAddLink {
    origin_url: String,
}

```

此时，要为刚刚定义的这个结构体定义转换方法，为此需要引入nanoid：
```rust
// 在文件开头添加
use nanoid::nanoid;
```

```rust
// 得到新链接的方法
impl ApiAddLink {
    fn to_new_link(self) -> Link {
        Link {
            tiny_code: nanoid!(5),
            origin_url: self.origin_url,
        }
    }
}
```

现在开始添加三个函数，
首先引入web对应的网络库和对应的json库；
```rust
use actix_web::{post, get, web::{Json, Path}, Responder, HttpResponse, http::header};
```

大概的东西和我之前学习的那些是差不多的，感觉不用特别去记忆。

通用的结构体用于向前端返回格式统一的数据：
```rust
#[derive(Serialize)]
pub struct ApiResult<T: Serialize> {
    pub ok: bool,
    pub err: Option<String>,
    pub data: Option<T>,
}
```

如果有需要，重新把视频看一遍。没别的办法，主要是。

