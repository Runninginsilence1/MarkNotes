# MyBatis使用指南
基于黑马的 JavaWeb的课程
# MyBatis基本信息
它是一个简化 JDBC的框架。
顺便提一个：虽然我觉得它和 GROM一样是，但是很遗憾的是 MyBatis并不是一个确确实实的 ORM框架。
它是一个**半ORM框架**。 Hibernate是全 ORM框架。
**MyBatis拥抱原生SQL。**

MyBatis 简化了大量 JDBC的工作，所以进行开发的话，使用 MyBatis就行。

# 快速入门（尝试整合 Spring 和 MyBatis）
步骤就是：导入坐标，配置文件xml，配置单独的表的映射mapper，然后代码调用。。。

```xml
    <dependencies>
        <!-- Spring的基本坐标 -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.2.10.RELEASE</version>
        </dependency>

        <!-- Alibaba的 druid：数据库连接池 -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.1.16</version>
        </dependency>

        <!-- MyBatis的基本坐标 -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.6</version>
        </dependency>

        <!-- mysql，这个好像是数据库操作的基本坐标 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.47</version>
        </dependency>

        <!-- Spring如果要操作数据库，需要的坐标 -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.2.10.RELEASE</version>
        </dependency>

        <!-- Spring 整合 MyBatis的坐标 -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>1.3.0</version>
        </dependency>
    </dependencies>

```

## 了解一下旧版本的配置开发
+ [ ] todo: mybatis的xml不加载我的 Properties，不知道为什么

首先是加载 mybatis的配置文件和对应的映射文件：一个是 mybatis-config.xml, 一个是 对应的mapper。

然后就是一系列的通用的代码模板。

## Mapper代理
说结论：建立Mapper接口**来统一SQL映射的规范**来可以减少硬编码，解耦合，以及方便 ide的提示；

Mapper类是对应的**MyBatis的原先Mapper配置文件的**，在快速入门的时候就已经发现要写字符串了。

![Img](./res/drawable/MyBatis使用代理开发的步骤.png)

这里有一个小知识点：
maven的项目编译后的**字节码文件里的项目结构同级**，换句话说 java和resources文件夹里面的**同名文件夹内文件最终会合并在一起**；

