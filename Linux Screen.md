# 是什么
screen可以启动另一个窗口用于执行耗时任务；
要实现类似的效果可以使用nohup & 命令和tmux（另外一个多会话终端）
# 基本使用

Ctrl+d+a可以在不关闭s的情况下恢复原终端


状态介绍
通常情况下，screen创建的虚拟终端，有两个工作模式：

Attached：表示当前screen正在作为主终端使用，为活跃状态。
Detached：表示当前screen正在后台使用，为非激发状态。
通常情况下，不需要关注上面的状态。

活跃状态不能直接通过r选项重连，不知道为什么

S可以新建一个screen并以对应的参数命名，
# 创建一个叫Hello的虚拟终端
screen -S Hello

使用-R创建，如果之前有创建唯一一个同名的screen，则直接进入之前创建的screen

使用-S创建和直接输入screen创建的虚拟终端，不会检录之前创建的screen（也就是会创建同名的screen)

ca可以组合其他键一起使用

# 常用操作
Ctrl+a+d可以挂机screen到后台
screen -r 可以重新连接，有自动补全
screen -ls 可以查看当前正在运行screen的情况；