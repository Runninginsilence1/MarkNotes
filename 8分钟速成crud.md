他这个项目所使用的nvm的版本：`8.12.0`

如果fetch项目的之后maven没有自动加载，右键pom.xml，当做它加载。

修改dev中的数据库连接信息；

新建inc库，字符集选择utf-8，排序gen...
然后右键运行sql文件，选择`inc.sql`

至此，可以选择直接运行启动类了。

前面这里是在介绍项目怎么启动，索性就不看了。

在mybatis的gencore那个项目中，修改资源目录下mine的incadmin的连接信息：`<jdbcConnection>`位置应该是；
这个玩意可以根据db的数据自动生成代码；创建好这个generator所需要的目录之后，可以直接生成代码，复制到原本incadmin的对应的位置；

![[Pasted image 20230503113909.png]]

然后重启Java后台，如果有出现了错误就会到这里来看，位置`05:49`

这个生成好像也可以自动生成页面的代码，在资源的js目录下；

添加完页面后，如果需要可以从主页跳转到这个页面，需要在router中添加页面路由，但是这种方式好像不会在菜单中刷新？

查询的话在对应的vue里面增加条件，即可增加条件。

所以这里添加菜单的功能并非是我通过代码添加，而是由低代码平台来进行添加；

先添加目录，再添加菜单，然后在角色管理中分配权限。

![[Pasted image 20230503114624.png]]

路径必须和页面路由对应。
![[Pasted image 20230503114746.png]]

然后在对应的页面管理的位置添加；

一个页面就算添加好了。


Vue框架中，在vue实例里面data属性里面声明的对象，底层似乎是Proxy，用于支持Vue的响应式；


elementUI提供了用于格式化日期的接口，效果是：
![[Pasted image 20230503195704.png]]


代码：
```js
<template>
  <div class="block">
    <span class="demonstration">默认为 Date 对象</span>
    <div class="demonstration">值：{{ value1 }}</div>
    <el-date-picker
      v-model="value1"
      type="date"
      placeholder="选择日期"
      format="yyyy 年 MM 月 dd 日">
    </el-date-picker>
  </div>
  <div class="block">
    <span class="demonstration">使用 value-format</span>
    <div class="demonstration">值：{{ value2 }}</div>
    <el-date-picker
      v-model="value2"
      type="date"
      placeholder="选择日期"
      format="yyyy 年 MM 月 dd 日"
      value-format="yyyy-MM-dd">
    </el-date-picker>
  </div>
  <div class="block">
    <span class="demonstration">时间戳</span>
    <div class="demonstration">值：{{ value3 }}</div>
    <el-date-picker
      v-model="value3"
      type="date"
      placeholder="选择日期"
      format="yyyy 年 MM 月 dd 日"
      value-format="timestamp">
    </el-date-picker>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        value1: '',
        value2: '',
        value3: ''
      };
    }
  };
</script>
```
