title: Python项目管理工具：虚拟环境venv使用方法
date: 2019-09-19 14:39:48
tags:
	- python
	- 工具
---


## 一、引言

众所周知，项目的环境管理一直是比较头疼的问题，如果有两个项目，一个用的是 a 库的 v1 版本，一个用的是 a 库的 v2 版本，那么如果 a 库是安装在系统环境下的，就会出现项目所依赖的库文件版本不对的问题。

Python 社区为了解决这个问题，提出了虚拟环境的概念。也就是人为的创建一个虚拟环境供项目运行，而该项目需要什么库文件，则自行在这个虚拟环境中下载安装，而在虚拟环境中下载安装的库文件，是不会影响到系统环境中的库文件的。有了虚拟环境的概念， Python 的项目管理也就不那么麻烦了。

接下来，我来总结下如何使用 Python 的虚拟环境。

ps: 以下的讲解都是在 Python3 的前提之下。

<!--more-->

## 二、venv

鉴于 Python3 中以标准库的形式包含了 venv 工具，因此就不再讲解 venv 工具的安装了。

不过 Ubuntu 环境是需要自行下载安装的，运行命令如下：

$ sudo apt-get install python3-venv

**1. 创建虚拟环境**
这里，假定你已经拥有了 venv 工具，我们直接打开命令行工具

Windows 平台下运行：

    py -3 -m venv venv

MacOS 或者 Linux 平台下运行：

    python3 -m venv venv

运行完这行命令，应该可以在当前目录下看到新建的一个文件夹，名为 venv，其就是虚拟环境的名称，并且以后我们在虚拟环境中所有的操作以及下载的库文件，都会在这个文件夹中。
![20180720185730986.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pLmxvbGkubmV0LzIwMTkvMDkvMTkvUm5QYjFXRFRndkdNa0N5LnBuZw?x-oss-process=image/format,png)

**2. 激活虚拟环境**
创建了虚拟环境却没有激活的话，你还是处于系统环境中。怎么激活虚拟环境呢？

Windows 平台下运行：

      venv\Scripts\activate

MacOS 或者 Linux 平台下运行：

     source venv/bin/activate

这里 venv 是你的虚拟环境的名字。运行完之后，自动会刷新命令行界面，以括号内嵌虚拟环境名字的形式提示用户，现在是处于 venv 虚拟环境中。

**3. 使用虚拟环境**
虚拟环境除了 Python 一些核心的包、pip 工具等等，其他都没有，因此，当前项目需要什么，则使用 pip 工具下载即可。

其他的使用方法，与系统环境下的使用没有什么不同。

**4. 退出虚拟环境**
退出虚拟环境很简单，各平台都是一样的：

    deactivate

从虚拟环境中退出到系统环境是很明显的，因为前面的虚拟环境名称提示符消失了。
![20180720190201469.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pLmxvbGkubmV0LzIwMTkvMDkvMTkvaEw4V1ZhMlMxbnhpckhDLnBuZw?x-oss-process=image/format,png)
## 三、在 PyCharm 中配置当前项目虚拟环境

venv 是很好用，但是我们该如何在 PyCharm 中配置当前项目所使用的环境呢？

1. 进入 Project Interpreter 界面
我们打开 PyCharm，菜单栏选择 File，点击 Settings，进入到设置界面，然后配置 Project Interpreter
![20180720190548991.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pLmxvbGkubmV0LzIwMTkvMDkvMTkvTUo1Z05kY3NIWXVBeDZTLnBuZw?x-oss-process=image/format,png)

2. 添加虚拟环境
在 Project Interpreter 文本框右侧点击齿轮按钮，点击 Add。
![20180720190956776.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pLmxvbGkubmV0LzIwMTkvMDkvMTkvZDFmNWNCWVdBQ0RlcXdJLnBuZw?x-oss-process=image/format,png)
然后，选择 Virtualenv Environment 标签卡，选择 Existing enviroment（选择上面那个应该可以直接新建一个环境，这里为了续接上一节中的环境，则直接选择了已存在的环境），这里，如果 PyCharm 没有找到你新建的环境，则需要你自己去打开文件找到你虚拟环境中的 Scripts 目录下的 python.exe 解释器文件。

3. 测试运行
最后点击 OK，即可看到环境列表多了一行。这里，我们可以在项目中切换各个环境进行自己当前项目的测试，还是很方便的。

四、总结
在系统环境中，使用最基本的 Python 解释器提供虚拟环境 venv 的功能，然后在各个虚拟环境中完成我们的项目，这样是比较好的项目组织方式。

