第一步：Spigot 启动服务
1. 首先下载核心spigot的核心jar文件

2. 确保放在路径中不包含非ASCII码（这里放到非中文目录）的文件夹内。该文件夹作为软件的工作目录。

3. 建立批处理文件（txt文件修改为bat文件），通过命令行来运行核心的java代码
代码如下
```cmd
@echo off
java -Xmx1g -Xms1g -jar spigot-1.19.jar
pause
```
简单的说建立一个文本文件，把后缀名改成bat，其中的内容是上面代码。本质是运行批处理命令，保存后这样这玩意就可以执行了

4. 第一次启动
第一次启动时日志会提示你需要同意elua.txt文件中的条例，然后程序强制退出；
接着就会出现一个eula.txt文件，文件内容改成eula=true

5. 第二次启动
第二次启动时控制台会打印大量的日志，说明软件正常运行。等待控制台停止输出之后（通常控制台打印的最后一行数据内容翻译出来是告诉你输入help可以查看命令帮助）

务必记住每次使用stop命令结束控制台并关闭服务器，否则可能出现服务器回档等问题

6. 修改server.properties文件
这是服务器的配置文件。如果盗版可以联机，修改online-model的选项为false：online-mode=false

7. 修改完成后再次启动bat文件就等于服务器已经开启了

第二步：使用Sakura frp进行内网穿透
1. 下载sakura frp软件
2. 输入密钥并登录
3. 


关于给Spigot(水龙头端)添加服务器模组的问题
首先是需要三个基本的jar文件：
forge的18.2的版本服务端

spigot 和 forge 是不同的服务器核心，前者支持原版服务器，但是不支持mod，也就是我前几天用的那个(六月二十号) 

六月二十号 单词：
earnings 薪水；工资
garden 花园；公园
dental 牙齿的；牙科的；
predictable 可预言的；意料之中的；
sustainable 可持续的；可持续利用的；
blame 责怪；
confusion 困惑；混乱局面；
sustainability 可持续性；耐久性
genuine 真正的；真诚的；
thorough (注意区别 though) 彻底的；完全的；
acting
afraid 害怕的；担心的
household 一家人；家喻户晓的
afraid 害怕的；
poet 诗人；
comment 评论；注释
profession 职业；行业
steep 陡峭的；过高的；急剧的
appearance 外貌；出现；起源；
fame n。出名；有名
fortune 财富；大笔钱；机遇
quantity 数量；大宗
farm 农场
bicycle 自行车
municipal 市政的
retire 退休；就寝；离开；
peaceful 和平的；
retire
steep
peaceful
municipal

### 六月二十号回顾Fragment的内容
Fragment
9.0版本后 使用Fragment请注意需要继承androidx包下面的Fragment，因为普通安卓包里面的Fragment在安卓9.0里面已经废弃
为了保持对高版本的兼容性

动态加载布局需要使用LayoutInflater这个实例的属性方法。

实战在Activity里面动态添加Fragment
(1) 创建待添加Fragment 的实例。
(2) 获取FragmentManager，在Activity 中可以直接调用getSupportFragmentManager()
方法获取。
(3) 开启一个事务，通过调用beginTransaction()方法开启。
(4) 向容器内添加或替换Fragment ，一般使用replace()方法实现，需要传入容器的id和待添
加的Fragment 实例。
(5) 提交事务，调用commit()方法来完成。

```java
import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.widget.Button
import androidx.fragment.app.Fragment

class MainActivity : AppCompatActivity() {
    private lateinit var button: Button
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        button = findViewById(R.id.button)
        button.setOnClickListener {
            replaceFragment(AnotherRightFragment())
        }
        replaceFragment(RightFragment())
    }

    private fun replaceFragment(fragment: Fragment) {
        val fragmentManager = supportFragmentManager
        val transaction = fragmentManager.beginTransaction()
        transaction.replace(R.id.rightLayout, fragment)
        transaction.commit()
    }
}
```


#### 关于Fragment的返回栈：
上面使用了一系列步骤通过代码动态替换了相关的Fragment
此时因为是直接替换的关系，因此直接使用back键后退的话会直接退出程序。
换句话说Fragment默认是没有类似Activity的任务栈的。
可以通过代码手动添加。
请不要忘记对Fragment的操作是通过一个事务实现的。
这个事务对象也提供了一个addToBackStack()的方法，

总之记得在事务里面加上一句该语句就可以实现Fragment的返回栈功能了。



#### Fragment与Activity的交互
虽然F嵌入在Activity里面，但是从代码的层面来说他们是两个独立的类，彼此是不能直接调用的

想要实现上面提到的功能：
1. 通过FragmentManager获取到Fragment的实例来实现从A到F

2. 在F中可以通过getActivity方法来获取A的实例。

3. 不同的Fragment之间进行交互的方式似乎有点滑稽：通过上面的方式实现一个 F → A → F的方式。。。

#### Fragment的生命周期
F的生命周期与A的大致相同，不过添加了几个附加的生命周期，直接从pdf当中复制
onAttach()：当Fragment 和Activity 建立关联时调用。
onCreateView()：为Fragment 创建视图（加载布局）时调用。
onActivityCreated()：确保与Fragment 相关联的Activity 已经创建完毕时调用。
onDestroyView()：当与Fragment 关联的视图被移除时调用。
onDetach()：当Fragment 和Activity 解除关联时调用。


#### 关于Fragment的实例化：
首先Fragment是可以直接实例化的，并且在安卓里面是可以这么做的。
因为F会事先在 Activity的布局文件里面声明 因此FragmentManager提供了一个findFragmentById方法来直接获得实例。。。

### Android中的SQLite数据库
复杂的关系型数据用数据库来实现


## 关于forge(可以用来开mod的服务器)
### 过程
1. 下载forge
2. 下载Java
3. 用forge服务器安装服务端
4. 或者用云盘服务端

### 关于服务器核心
顾名思义：服务器核心就是开服的工具，没有他就没有服务器。
纯净版（即原版的服务器） 这些服务端都装不了mod
我们使用forge来安装mod
为了适应我的客户端我应该安装的forge的版本应该是1.18.2

