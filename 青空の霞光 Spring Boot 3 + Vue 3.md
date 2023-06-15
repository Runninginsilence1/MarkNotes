我觉得他这里就讲的很清楚：前后端分离就是前端一个服务器后端一个服务器嘛，所谓服务器开发其实本质就是后端开发。

# 环境搭建
他这个版本有点太高了说实话，选择jdk17
Boot 3.5（我这里只有3.6给我选不知道为什么）
选择Spring Web安排上
SQL -> Data jdbc, Mysql Driver
Mybatis...它没有用plus。
![[Pasted image 20230506134636.png]]

使用create-vue来创建项目
npm install -g create-vue
![[Pasted image 20230506135323.png]]

这毕竟是毕业设计，所以我不搞复杂了。所以不使用git。

# 后端设计
总之是先设计后端的需求；先实现一些简单的功能。
![[Pasted image 20230506144357.png]]


然后这个boot项目搭建好之后，不配置数据源（似乎web项目必须要这样）是无法启动项目的。
![[Pasted image 20230506145202.png]]

根据控制台信息似乎是boot框架会自动加载一个DataSource的bean（就是class啦），而因为没有数据源信息所以这个东西初始化失败了。

搞定数据源配置之后运行就没问题了。


# 配置spring security
配置spring security的相关配置：
```powershell
# 在项目后端根目录中使用：
$dirPath = "./src/main/java/com/example/backend/config"
New-Item -ItemType Directory -Path $dirPath
```

对的，这样就可以一键建好目录了，cv正是效率提高的有效表现。

SecurityConfiguration

添加两个注解：
@Configuration
@EnableWebSecurity

...基于Security并非我现在要加紧做的功能，所以把相关的代码放在这里，到时候去问gpt吧。

专门提一下，前后端统一的restful风格，比如权限登录这里就用：`api/auth/login`这样。

```java
    public SecurityFilterChain filterChain (HttpSecurity http) throws Exception {
        return http
                .authorizeHttpRequests()
                .anyRequest().authenticated()
                .and()
                .formLogin()
                .loginProcessingUrl("/api/auth/login")
                .and()
                .logout()
                .logoutUrl("/api/auth/logout")
                .and()
                .csrf()
                .disable()
                .build();

    }

```

gpt告诉我这段代码就是用来进行安全规则配置的。那么暂时就不要了解太多。

提交完这段代码之后启动项目测试一下。
这个页面我都不知道怎么出来的我去...
![[Pasted image 20230506165223.png]]

就大概是这么个样子。

然后因为要添加防止一个重定向的问题，所以需要添加一个successHandler，来适应前后端分离的需要：如果用apipost的话可以获取到一个数据之类的东西；


我为什么要弄security这个东西呢？其实因为他这里的需要。这个没办法。

通过apipost测试时确实可以显示通过writer显示的自定义信息，但是不符合json的规范，所以接下来定义一个json的相关java实体

# 通过lombok定义通用返回对象
理一下核心的思路：
通过引入一个库：fastjson，然后通过对应的方法，将java pojo实际转换成json格式的相应。

看那个视频的这部分内容。`00:24:14`


说实话，这里我没有学到什么新的东西。

....

有需要就回来看吧。

总结一下：就是通过设置一个R对象，还有配合fastjson来像前端返回一个json字符串。
如果是在boot的Controller层的方法里面返回obj，那么不需要额外一个fastjson来处理；
可是（我的理解），在security中设置返回结果时，不会自动使用Jackson进行处理，所以需要fastjson来进行一下额外的处理。这是后端迁就前端的一个部分。（因为前端不需要对json进行额外的处理）


如果要通过security来进行用户登录的验证的话，需要通过
`UserDetailsService`这个接口。

gpt的介绍是：
```
UserDetailsService是Spring Security框架中的一个接口，它用于加载用户信息并返回一个UserDetails对象，以供Spring Security进行认证和授权。

在Spring Security中，认证是指验证用户的身份，通常需要检查用户提供的用户名和密码是否正确。授权是指决定用户是否有权限执行某个操作或访问某个资源。

UserDetailsService接口定义了一个loadUserByUsername方法，该方法接受一个用户名作为参数，并返回一个UserDetails对象。UserDetails包含了用户的身份认证和授权信息，如用户名、密码、角色等等。在实现loadUserByUsername方法时，开发人员需要根据用户名从持久化存储中获取用户信息，并将其封装成UserDetails对象返回。

Spring Security提供了多个实现UserDetailsService接口的类，开发人员可以根据自己的需求选择合适的实现类。例如，JdbcUserDetailsManager可以从关系型数据库中加载用户信息，InMemoryUserDetailsManager可以在内存中保存用户信息，LDAPUserDetailsService可以从LDAP服务器加载用户信息等等。

在使用Spring Boot和Spring Security时，可以通过实现UserDetailsService接口来加载应用程序中的用户信息，并在SecurityConfig中配置认证和授权规则，以保护应用程序中的资源免受未经授权的访问。
```

接着就是为登录注册的部分撰写相应的后端代码和数据库代码了。
需要准备实体类account，对应的dbmapper，还有业务的接口service


在db设计时，因为是使用username或者email来进行登录，所以为他们加上了unique。

搞清楚核心的逻辑，而不是代码怎么写的。

他这里的mapper直接通过注解来写sql语句，执行对应方法就相当于执行语句；
```java
import com.example.backend.entity.Account;
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Select;

@Mapper
public interface UserMapper {
    /**
     * 这个在验证的时候可能会有潜在的bug，所以唯一性需要还要对应上那一部分。
     * @param text
     * @return
     */
    @Select("select * from db_account where username = #{text} or email=#{text}")
    Account findAccountByNameOrEmail (String text);
}
```

快速过掉吧。
....
通过Security对接数据库这个部分，我觉得是之后在项目不能运行的情况下再去考虑的。


总结：借助 Security, 已经基本完成了用户登录的功能（后端）。

# 前端登录页面
以`create-vue`这个脚手架为基础，来创建前端项目。

先直接把页面给弄出来： vue里面写好样式。


`App.vue`中可以设置基本样式，但是又要在index.html里面去修改，我不知道这个是什么原理

vue感觉就像积木一样。这也是历史悠久的一种做法。

# vue项目安装三方模块
我不知道
`npm install element-plus --save`
`npm install vue-axios --save`

加入`--save`这个选项后，类似于pom.xml中，会出现在dependencies里面

有问题：
视频里面可以自动出现el组件的标签提示；
而我这里是没有的。

就像前面的Security一样，我这里只是按照它的步骤来的：按需引入element-plus的组件，这样可以使打包的体积减少。

省事就完整引入，大小击败！kb。

这里写一下它按需引入的步骤：

首先使用npm来导入插件。
`npm install -D unplugin-vue-components unplugin-auto-import`

然后在vite.config.js中引入以下的东西；
![[Pasted image 20230507165600.png]]

这一步我成功了。正常引入图片了。

至少以上，可以搭配我的基础知识和三方组件来做东西了。

这里提到要实现这里的这些页面的跳转的话，就需要使用多级路由来实现了。

关于样式的话，我不想浪费太多的时间，所以直接拷贝别人的样式最好。

所以这里主要是表单怎么写：
输入框模板：`<el-input type="text" placeholder="用户名/密码"/>`

如果要在模板框这里引入图像(比如说头像什么的)

让我们来看看他这一步做了什么：
1. vue开始的script里面导入：`import {User} from '@element-plus/icons-vue'`
2.  然后在 `<el-input>`里面引入了一个叫做插槽的东西，总之代码是这样：
```js

```

使用idea各种没有提示，我想vscode肯定也是要配置的。

`<el-divider>`这个标签似乎就是一个分割线，类似于这个效果：
![[Pasted image 20230507215752.png]]

 总结：这里和我前面学习的前端的知识没有什么大的区别，主要就是一个slot这个功能特性的使用；虽然我到现在都还没有理解这个东西的意思。


然后是`<router-view/>`这个东西，是vue的一个组件，只要在模板里面插入这个标签，那么vue渲染页面的时候会自动根据这个路由的规则（去路由规则里面去找），找到了就直接渲染。

![[Pasted image 20230508134457.png]]

还是没懂，所以直接实际操作一下试试。

Vue初始化脚手架的Welcome页面，涉及到哪些文件呢？
`App.vue` 中只保留 `<template>` , `<script setup>`, `<style scoped>` 这三个标签。
移除Views中的所有的样式（应该路由样式）组件。
然后router中把常用router的值设置为空数组。
涉及`index.html`和 `main.js`。


需要整个项目全局引入element的话，要在main.js里面引入它的样式。

# 前端路由
多级路由和嵌套路由。

首先页面之间通过路由来跳转。
然后是文件的结构的规范：
views中存放和路由有关页面视图，相当于“索引”。
然后components里面存放。

所以路由就是根据路径匹配，然后在 `<router-view/>` 里面渲染对应的组件出来。

路由我差不多搞懂了。就是路径绑定组件。

# 登录登出功能
前后端分离后，流程是：
前端 -> 后端 -> 数据库

前端通过axios来发送异步请求。
在 `net/index.js` 中封装一下axios的api。

之前安装了一下 `vue-axios` 这个东西，但是它是axios的封装，也就是说最重要的axios是没有安装的，所以需要安装一下。

然后前后端分离后，前后端都是用来进行表单提交的。

登录功能：
问题描述：
用户提交用户名和密码，正确则返回正确结果，错误则错误结果。

需求分析：
使用表单来提交用户输入的相关数据；
后端接受前端的功能；

前端：
需要在“登录功能”的组件里面引入相关的逻辑，然后提交post请求；
然后需要在main.js里面配置一下axios的配置，这样axios与其交互的后端服务器的相关配置就定义好了。

首先在vue3.0中可以直接通过这个类似于以下的这种方式来设置渲染页面时需要使用的响应式数据；
```js
<script setup>
import {reactive} from 'vue'
import {ElMessage} from 'element-plus'

// 定义表单对象
const form = {
  username: '',
  password: '',
  remember: false,
}

// 通过箭头函数定义函数对象
const login = () => {
  if (!form.username) {
    // alert('username is empty, but...')
    ElMessage.warning('请填写用户名和密码！')
  }
}
</script>

```

然后之前封装的post的api，涉及到回调函数相关的用法。 这样登录功能基本就算成功了。

这样我基本也明白了一个所谓登录注册的逻辑。
所以🎉🎉
我总算也算是vue已经入门了。

此时如果你跟着那个登录页面去写的话，那么基本上前端登录的问题就已经解决了。

不过此时如果在浏览器里面搞一个这个东西，会出现一个跨域的问题。
需要在Spring Security里面进行一个修改。

配置好跨域之后，实际上就完成整个登录的系统了。
