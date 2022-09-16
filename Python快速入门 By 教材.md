# Python快速入门 By 教材
# 环境
## Anaconda和Python环境
可以粗略的把Anaconda理解安装有大量三方库的Python，所以直接安装 Anaconda就相当于Python安装好了。

在配置anaconda的配置文件之一（可能）的.condarc和Python本身的游标卡尺一样恶心

直接用默认的成功了。。。

```shell
conda config --remove-key channels
conda config --show channels
```


conda create -n my_learn_python39 python=3.9

下面的都是之前临时使用的东西

```yaml
channels:
    - defaults
show_channel_urls: true
channel_alias: https://mirrors.tuna.tsinghua.edu.cn/anaconda
default_channels:
    - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
    - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free
    - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
    - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/pro
    - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
custom_channels:
    conda-forge:https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
    msys2:https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
    bioconda:https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
    menpo:https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
    pytorch:https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
    simpleitk:https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
```


```shell
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/msys2/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --set show_channel_urls yes
```

## Anaconda3的进阶学习可以通过官方文档
地址在书上，什么时候再去补齐
此时 Anaconda的部分差不多就完成了

## vscode相关的搭建
首先安装 vscode，很简单
需要安装相关的插件：
安装 Pylint，一个Python代码检查工具，使用 pip安装，pip是一个python的包管理工具

强大的游标卡尺：YAPF，可以一键格式化代码为 PEP8 规范。
安装shell命令：pip install YAPF
同时还要去vscode里面应用这个包：settings的 "python.formatting.provider", 选择yapf


这里直接就介绍git了啊，还挺方便的，不过我就不会在这里重新写一遍了。

至此，环境已经全部配置完毕。

# 一个爬虫实例？

# 草稿区域

# 函数，模块和包
书上一个很清晰的解释
函数的概念不需要解释；
把相关联的**函数代码放在一个文件**里面，并命名为.py, 这个文件就是一个Python模块。**类也在模块中定义**
将模块放在一个文件夹里面，并配置一个__init__.py配置文件，这个文件夹就是一个python包。

def 用来定义函数，类似fun和func，格式很简单，不用刻意去记

连续三个双引号代表什么？
注释，定义多行字符串
