# 主流Spring技术学习 黑马
看的黑马的视频
# Spring的基本介绍
这部分的内容应该可以当做一个索引或者说目录来用：
1. Spring的架构
1. Spring的核心容器：IOC和DI
## Spring架构
![Img](./res/drawable/Spring4的架构.png)

Spring的核心思想就是IOC和AOP，这两个重点学习。
解释一下图中数据集成的意思：Spring自身操作数据库的能力尚可，并且它**对其他框架或者组件的整合也非常的优秀**。这就是集成的意思。


## 核心容器
### 传统 JavaEE的问题
IOC的核心理念是：接口的实现是一种硬编码，导致代码的耦合度高；但是不写实现的话程序优惠无法运行；

所以核心在于实例对象的来源上 -> 因此提出改进思路：对象的实例化**不再由程序员通过代码来进行控制**，而是转由 程序(Spring)来进行提供。
肯定用到了**反射**不用想。

相比于 重新编译的代价，反射增加的性能损耗几乎可以不计。

### IOC
直接贴个图片在这里好。
![Img](./res/drawable/IOC的核心概念.png)

IOC是**解耦的实现**，DI是**对IOC的补全**。

### IOC的实际案例
大概就是这么一个流程：
1. 定义需要由Spring管理的类
1. IOC管理你的类
1. 在业务中获取IOC容器的实例
1. 通过IOC容器实例获得实例类

那么首先导包：
```xml
<!-- pom.xml中 -->
<dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.2.15.RELEASE</version>
        </dependency>
```

在配置文件里面通过 bean标签配置类，直接写个实例即可。
重要的就两个属性：id表示到时候获取时候的名字，class表示类路径。

然后在代码里面过一下代码就行
```java
public class App {
    public static void main(String[] args) {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
        User user = (User) applicationContext.getBean("user");
        System.out.println(user);
    }
}
```

正式的业务没有这么写的，因为这是个入门案例

### DI的快速入门（setter的引用类型注入）
两个类之间产生依赖关系（**最直接的就是一个实例中包含包含另一个实例的引用**），使用DI在代码方面进行解耦合。

在上一个 bean的标签里面 添加一个`property的标签`
里面的属性分别是：name 属性名，需要写类定义的属性（注意**区分bean 和 使用代码定义成的类**）；ref 绑定bean，值是 bean的id

## 关于Bean
Bean还可以两(?)个额外的基础配置：
- 设置别名
- 控制单例多例

通过 标签属性即可控制；name 和 scope，简单不说了

### Bean的实例化方式
三种，重要的是**广泛应用的工厂模式**
第一种，就是默认的，通过**反射获取构造器的构造器**； 具体后面应该细嗦
第二种，静态工厂配置，添加一个`factory-method`属性（里面写）让 spring知道这是一个 静态工厂；
第三种，实例工厂配置，和第二种不一样的就是需要先实例化工厂才能获取产品的实例：那么先用普通的bean配置工厂，然后使用额外使用`factory-bean`属性指定工厂bean获取对象。

第三种可以优化：
优化成一个步骤：
Spring提供一个接口，这样实现接口就可以**自动识别成一个工厂**，配置时就可以使用默认配置来简化配置文件的书写了。

接口本身比较简单，自己去看

### 生命周期
先贴图
![Img](./res/drawable/SpringBean的生命周期.png)

可以配置生命周期的方法：
两种：一个在配置里面写，第二个是**代码实现接口**（相当于符合spring的规范），这样spring就会**有一个默认的处理机制**
比较简单，需要的时候去查


### DI具体
就是给bean的**成员变量赋值**。

setter和构造器两种；
setter：
property标签里面 value表示基本类型， ref加bean的标识 表示引用类型。

构造器：
换了个标签的名字： constructor-arg。这个就是所谓的标准写法；

不过这个标准方式要求**配置和代码的名称要一致**，所以耦合度较高；
有另外一种，根据类型，这种就要求**顺序一定**了。通过type属性确定；这种解决了**形参名称耦合的问题**

另外还有一种**使用参数位置的那种**，使用 index属性确定；

---
以上的方式都各有各的使用方式；那么该怎么选择？

![Img](./res/drawable/依赖注入方式选择.png)

我一直在想**多个构造器**的情况该怎么办...
以后应该可以**直接使用注解**，所以前面的xml的配置可以**适当了解**即可。

### DI自动装配（我叫自动DI，或者自动装配）
DI需要在bean里面去手动配置，不方便，所以 提供了一个**自动装配**的功能

添加属性`autowire`即可。
不过好像注入的属性**也需要用 IOC管理**

![Img](./res/drawable/自动装配的细节.png)

### DI集合注入
为什么不能一起讲完。。。
针对这几类的注入：数组、List、Set、Map、Properties

通过**子标签**来完成

具体的操作去查，现在不看🙈

### 一个小案例
第三方的包的实例使用 容器管理的时候，**推荐使用setter注入**。就酱。

### Spring加载 Properties的配置
呃讲了一个 Spring 名称空间的问题，虽然我也不知道为什么：到时候直接去看；
小知识：怎么给 Spring添加新的命名空间：需要的时候可以直接去看视频；

![Img](./res/drawable/Spring的Properties的加载流程.png)

提了一个小知识点：上面的`location`写`classpath:*.properties`
意思是**只读当前项目的所有配置文件**。

![Img](./res/drawable/加载多少Properties.png)

## 容器本身
最后系统的学习一下容器本身。
容器加载和bean的获取不说。

![Img](./res/drawable/Spring中容器类的层次结构.png)

顶层接口的容器的**所有bean都是延迟加载**

## 注解开发
主流方式。

Spring 2.5的时候好像是一个半注解的方式，如果有需要直接看视频；

`@Componet`就能标记一个类为 Bean。他还有其他的别名：为了方便**看懂分层**；

介绍纯注解开发的方式：
最大的改变：配置文件的xml已经**完全被 Java类代替；**
注解`@Configuration`配置；
注解`@ComponetScan`指定 Bean的扫描路径；

使用 Annotation...来加载配置类；

总结一下的话把**原来标签的属性名都换成注解了**

生命周期方法也改成 注解了，在方法上面加上即可。

注入也一样了，不过好像**没有 setter注入和构造器注入了**
全部使用注入装配，**底层原理是 暴力反射**
在成员变量上写上`@Autowired`注解即可。
默认不写就是**按类型装配**
如果有多个实现类就使用注解`@Qualifier`来指定名称。

`@Value`对基本类型进行注入
使用它的好处在于**可以使用 Spring的配置占位符**

`@PropertiesSource`注解加载配置文件，但是**不再允许使用通配符**

### 第三方Bean
怎么一管理三方bean就又回到最原始的代码方面去了？？？
不过还是比最开始的那种强，这个主要是**自己重写第三方组件的实例获取方式**
然后 添加`@Bean`注解

介绍了加载配置类的方法： 使用`@Import`注解

第三方 bean也有DI的需求，怎么搞？
简单类型：通过**配置类的成员变量**打`@Value`注解，简单粗暴

引用类型：直接在函数的形参添加， Spring会扫描为自动装配（按类型扫描）；

## Spring 整合 MyBatis
### 回顾 MyBatis的快速入门
首先回顾 MyBatis的使用过程：
1. 配置文件
1. 映射文件（可以通过注解代替）
1. 模板代码： SqlSessionFactory
1. 执行业务

### 具体步骤（采用注解开发）
这是一个具体并且模块化的动作....
说实话我记得我原本是要做web来着，现在却变成一个劲的学框架了...
以上是吐槽。

大概步骤：
1. **整合所需要的**坐标
1. Spring的Bean管理 SQLSessionFactory以及进行对应的 DI，并提供**对应的配置信息**
1. 核心是 MyBatis的两个配置类： (待补充)

## 整合 JUnit
因为我之前其实没有怎么学过 JUnit，而我又是投了测试的岗位，所以这里的笔记**会写得稍微详细一点**

导包的话一样的： JUnit本身的，以及 Spring 整合 JUnit的包；
```xml
        <!-- JUnit 的坐标 -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
        
        <!-- Spring 整合 JUnit 的坐标 -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>5.2.10.RELEASE</version>
        </dependency>
```

然后在**测试包里面添加测试方法**，这个我以前没做过，都是直接用的注解；

配置`@RunWith(SpringJUnit4ClassRunner.class)`注解，表示这是测试类；
配置 Spring的配置类：`@ContextConfiguration(classes = SpringConfig.class)`

剩下完成自动装配，然后就是我熟悉的部分了...

一句话解释：要使用**Spring专用的类加载器**
![Img](./res/drawable/Spring整合JUnit的代码.png)

# AOP
一种**编程思想**。
面向切面编程 **指导开发者如何组织程序的结构**
最大的特点（或者说作用）： 不改动原有的代码， 做功能增强
说实话第一次看的的时候挺震撼...

## AOP的核心概念
我要把 AOP 的作用贴到这里： **不改动原有的代码， 做功能增强**
连接点：你AOP不是要增加功能吗？ 在哪些地方你**可以增加**，这些地方就叫连接点；
切入点：增强了之后，这些**已经被增强的方法**， 就叫切入点；
通知：需要添加的**新功能**就称之为通知
切面：一个通知与一个切入点对应之后，管**它们的绑定关系**叫切面
通知在代码中的本质是一个方法(Kotlin中函数是一等公民, 不知道这个会不会发生变化)，方法需要**依附于一个类**, 这个类叫做 通知类

![Img](./res/drawable/AOP的核心概念的介绍.png)
![Img](./res/drawable/AOP核心概念总结.png)

## AOP快速入门
**注意!** 注解算源代码的一部分, 所以如果**配置类的东西 还是用 XML比较好**

大概的步骤是: 导包, 连接点, 通知类和通知定义, 切入点, 定义切面. 完成.

第一步: 导包: context包如果导入的话不再需要 aop, 两者有依赖关系; 只需要导入 ASpectJ的包
```xml
    <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.9.4</version>
        </dependency>
```

我在你已经知道 AOP核心概念的基础上来直接说这个代码的实现: 

第二步: 连接点: 
**Spring里面一般把 函数(方法)作为连接点**

第三步: 定义通知类:
好像现在 Spring框架可以直接定义 一个 aspect, 不过暂时不管

通知(公共方法)定义, 上面加上你需要执行时机注解:`@Componet` 和`@Aspect`; 前者标注bean，后者标注该类**是一个通知类（？）**

第四步: 定义切入点
通过注解`@PointCut`定义一个**私有方法**, 里面有各种切入点定义的格式; 
比如 `@Pointcut("execution(void com.dao.UserDao.update())")`

第五步: 创造切面
第三步完善, 添加注解(应该是时机),需要把第四步定义的切入点作为注解的值;

并且 Spring的aop需要用 作为 Bean被 Spring 管理才行. 在Spring的配置类里面, 添加`@EnableAspectJAutoProxy` 注解, 表示开启注解配置的 aop


以上所有的内容, 整理成代码就是: 
通知类
```java
/**
 * AOP 通知
 */
@Repository
@Aspect
public class MyAdvice {
    // 定义切点: 指定执行哪个方法的时候进行 发送通知(应该是)
    @Pointcut("execution(void com.dao.UserDao.update())")
    private void pt(){}

    @Before("pt()")
    public void method () {
        System.out.println(System.currentTimeMillis());
    }
}
```

## AOP的执行流程
![Img](res/drawable/AOP的工作流程.png)
核心是代理模式。这个早就知道了，所以具体的就不写了。

## AOP切入点表达式
    `@Pointcut("execution(void com.dao.UserDao.update())")`
这个逼东西就是切入点表达式：因为做的是**无入侵式的AOP**，所以就只能**手动指定**切入点的特征了；

> 切入点：要增强的方法；切入点表达式：这个方法的描述方式

贴图片在这里：
![Img](res/drawable/AOP的切入点表达式匹配规则.png)
并且记住开发的名称要按照规范来进行开发：
![Img](res/drawable/AOP的切入点表达式的书写技巧.png)
这也是 Spring**约定大于配置思想**的最好体现。

**如果AOP没有生效**，那么很有可能是 AOP切入点表达式和连接点不匹配，
Spring会当做**没有匹配成功**，所以**不会给你报错**。

## AOP的通知类型
切入点表达式解决给谁加的问题；通知类型决定在哪里加：
有五种通知类型，前三种就是（前，中，后），后面两种返回后和抛出异常后。

环绕的执行方式和 前后置的应该是不一样的，**环绕要处理返回值的问题**；
贴一个代码，写在注释里面
```java
// 定义切点，注意返回类型和形参类型一定要写清楚
    @Pointcut("execution(int com.controller.MyController.update(int))")
    private void pt2(){}
...
// 定义通知类型为环绕
    @Around("pt2()")
// 通过类似占位的方式来表示 原始的方法执行的时机
// Object表示 原始的方法的返回值， 形参表示在通知函数里面把原始方法作为一个实例来对待
// proceed()就会执行原始的方法
// 如果原方法有异常抛出，这里也必须处理
    public Object foo2(ProceedingJoinPoint point) throws Throwable {
        System.out.println("我要把你的妈妈抓走");
        Object proceed = point.proceed();
        System.out.println("我把你的妈妈救回来了");
        return proceed;
    }

```

贴一个图片
![Img](res/drawable/AOP的Around通知类型的注意事项.png)

另外这个 ProceedingJoinPoint 实例还有一个 signature成员（翻译为签名），可以拿到 target每次执行后对应的信息：一系列的 get方法，不写了。

## 通过 ProceedingJoinPoint接口及其实现获取 Target（原始对象方法）的数据
API调用，暂时不写；



# SSM部分
首先说结论: SpringMVC是用来替换 Servlet复杂的开发流程的.

![Img](./res/drawable/SpringMVC的Web工作流程.png)

## 快速入门
坐标: Servlet 和 springwebmvc

```xml
        <!-- Servlet的坐标, 可能是因为 MVC 的底层就是 Servlet-->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>
            <scope>provided</scope>
        </dependency>
        <!-- SpringMVC 必备的坐标-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.2.10.RELEASE</version>
        </dependency>
```

定义控制器: 
现在 Servlet 相当于有了一个新名字: Controller
注解`@Controller` 标注为 Bean

里面的方法, 注解`@RequestMapping`, 表示处理对应url的资源;
`@ResponseBody`表示返回值的类型是请求体;

因为是 Spring技术, 当然需要定义 Spring的配置类;
和之前一样, 标注 配置和 包扫描路径即可;


**现在还没有SpringBoot**, 你的服务器依然是使用 Tomcat 启动的,**要让 Tomcat识别**,
到这里...

继承一个 配置类, 重写对应的方法,**相应的表示 Tomcat应该怎么对 SpringMVC**做配置
```java
public class ServletContainerInitConfig extends AbstractDispatcherServletInitializer {
    // 这里是获得 上下文对象的函数，注册 Spring的配置类, 然后返回
    @Override
    protected WebApplicationContext createServletApplicationContext() {
        AnnotationConfigWebApplicationContext ctx = new AnnotationConfigWebApplicationContext();
        // 这里填入 Spring的配置类
        ctx.register();
        return ctx;
    }

    // 在这里配置由 MVC 拦截的 资源请求路径，匹配规则和 urlPattern一样
    @Override
    protected String[] getServletMappings() {
        // 这里的意思是： 全部由 MVC管理
        return new String[]{"/"};
    }

    @Override
    protected WebApplicationContext createRootApplicationContext() {
        return null;
    }
}
```
这个配置类的本质是**你为Tomcat提供你要它管理的实例对象**, 所以方法的名称一般都是 get开头，意思就是 tomcat从这里拿；
里面一般就自己初始化对象然后返回...

我在想这里是不是也可以用 Spring来优化？因为这里不也是硬编码吗？


直接贴一个图片好了...
![Img](./res/drawable/SpringMVC的快速入门的总结.png)

### 快速入门的流程分析
![Img](./res/drawable/SpringMVC的入门案例工作流程的分析.png)

## Bean的管理
SpringMVC是 Spring的子技术，它们两个**都有Bean管理**，所以要避免 Spring的Bean加载 MVC管理的 Bean；

注解可以定义过滤的配置，具体解释一下，因为使用没有那么傻瓜式

`@ComponetScan`里面可以传入多个值，有一个就是 **排除过滤器的配置**，类型为 `@ComponetScan.Filter`， 指定过滤的类型和规则即可，贴一个代码就可以理解：
```java
@Configuration
//@ComponentScan("com.runninginsilence.controller")
@ComponentScan(value = "com", excludeFilters = @ComponentScan.Filter(
        // 表示过滤注解类型
        type = FilterType.ANNOTATION,
        // 结合上一个：什么注解？
        classes = Controller.class
))
...
```

**并且注意！这里很有可能忽略！**
标注了 `@Configuration`的类也会被其他的扫描到，并 **一并加载里面的bean**，所以过滤的**排查要做的彻底一点**

同时加载 Spring容器的那个对象也可以简化，**简化为只需要输入类名即可**。这里不写，因为是简化操作。

## 基于SSM的Web知识回顾：请求
### 请求路径的管理
就是 Request的来源；因为每个 Controller的功能可能相似，要用同一个路径名方便管理，但是同名又会冲突，所以: 
**RequestMapping也可以表示在 Controller上表示路径前缀**

### GET和POST请求发送参数
在原生的 Servlet里面读取参数特别麻烦， MVC直接通过**函数的形参**来解决这个问题。
没有必要写示例代码

**注意**使用POST请求的时候， 测试工具的 Request的头部**要设置数据类型**

而且传输中文数据的时候仍然有**乱码的问题**存在...
记录一个小疑惑：为什么**这里是用过滤器**做的，而我学普通 Web的确实直接设置编码？

不过需要**用过滤器解决这个问题的话**，需要在**初始化容器**的代码位置 初始化过滤器；
直接贴代码和注释笔记：
```java
@Override
    protected Filter[] getServletFilters() {
        // MVC默认帮你实现一些 常见的过滤器，直接使用他们
        CharacterEncodingFilter encodingFilter = new CharacterEncodingFilter();
        encodingFilter.setEncoding("UTF-8");
        return new Filter[]{encodingFilter};
    }
```
总结就是：Spring-Web中提供了专门的字符过滤器；

### SSM的参数传递
对于 后端而言，如果不是json的话，一般有**五种类型的参数形式**
1. 普通类型（包括String）
1. POJO
1. 内部包含嵌套POJO的对象
1. 数组
1. 集合

框架一般默认可以帮你实现**名称匹配的类型自动装配**
如果名称不一样，需要注解`@RequestParams`来绑定；

POJO可以实现**根据名字自动装配**，嵌套的话前端传参的时候要**用点连缀**, 比如 addr.position

集合的话和数组不一样，需要使用注解 `@RequestParams`，虽然我不懂原理...

### 传递JSON数据（这是个重要的内容，不过内容确实有点少）
现在流行前后端分离，所以**使用JSON数据传输数据更为流行**
MVC默认不带处理 JSON的库，所以需要手动导入：
```xml
<!-- 用于 JSON数据绑定的库 -->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.9.0</version>
        </dependency>
```

**注意！**虽然 MVC不带这个库，但是 Boot带！如果再次导入这个包会导致**Tomcat 服务器无法启动**

从这里我知道一个事情： 不能完全等同 mvc和boot，并非直接简化；小心半天运行不了！

MVC默认不能处理 JSON数据，但是可以通过开启一个注解完成这个功能：
`@EnableWebMvc` 可以开启一系列增强 mvc的功能，这里暂时只是开启json
然后在 Controller的形参前面加入：
`@RequestBody` 注意区分和前面的 `@RequestParams`，这次表示数据在**请求体里面**

还可以传递日期型的参数，不过暂时就不写
wait editing...

## 基于SSM的Web知识回顾：响应（可能是我一直想知道的内容）
在后端处理完数据后，就考虑往前端发送数据了，一般两种：
- 相应页面；
- 异步请求的话就是返回数据了。

之前我一直不知道 Controller怎么响应对应对应的静态资源，现在我知道了：
**返回的 String就是静态资源的路径**，这是mvc处理的；
如果加 `@ResponseBody` 才是返回响应体；
这个注解的作用是：
控制器会**自动把返回值的值解析成资源路径**，而这个注解就是设置成不要解析。

这是怎么会是呢？Servlet是底层原理；
MVC是框架，他在**底层自动会解析返回值**，通过 接口`HhhpMessageConverter`来转换成 Web相应的信息；
一个常见的实现类就是： Jackson这个库的实现类：他可以实现 POJO 与 JSON的互相转换。



# Maven高级
wait edit...

# SpringBoot
## 快速入门
SpringBoot 旨在简化 Spring应用的开发。
对于MVC应用，它通过**maven提供的parent继承的功能**继承配置，还有一个**启动容器用于集成启动**，这就是他可以快速开发的原理：牺牲自由性，通过**约定大于配置**的思想实现快速开发。

