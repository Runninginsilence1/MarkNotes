参考： [# 使用go语言开发自动化脚本 - 一键定场、抢购、预约、捡漏](https://www.leftpocket.cn/post/others/auto_booking/)

首先是这个curl命令的解析：  
-H是表示request里面写入一个请求头。  
  
data raw表示就是原始的json数据。  
  
看看自动生成的代码  
首先根据Payload的情况来定义一个结构体。  
本质就是一段json数据。  
  
实例化一个对象叫做data  
Marshal这个方法可以把go的结构体转化为json字符串。  
  
是的，query的逻辑我大概已经弄得很清楚了。

wait update