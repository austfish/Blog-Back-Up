---
layout: post
title: python完整项目打包Docker入坑记录
date: 2019-08-22 09:29:49
tags:
	- python
	- docker
---
第一个问题：为什么用docker
第二个问题：docker是什么
第三个问题：怎么用docker
第四个问题：怎么安装docker（linux）
第五个问题：将python打包到docker上
第六个问题：docker运行项目的相关操作
第七个问题：生成镜像报错后的none镜像怎么删除
<!--more-->

#### 第一个问题：为什么用docker
在使用Docker部署安装之前，我们还是先认识下Docker的优点:

* 软件构建容易，分发简单
* 应用得到隔离，依赖被解除
* 可以完美地用于 CI/CD
* 快速部署，测试完以后销毁也方便

* * *


#### 第二个问题：docker是什么
Docker的三个概念 
**镜像（Image）**：类似于虚拟机中的镜像。任何应用程序运行都需要环境，而镜像就是用来提供这种运行环境的。例如一个Ubuntu镜像就是一个包含Ubuntu操作系统环境的模板，同理在该镜像上装上Apache软件，就可以称为Apache镜像。

**容器（Container）**：类似于一个轻量级的沙盒，可以将其看作一个极简的Linux系统环境（包括root权限、进程空间、用户空间和网络空间等），以及运行在其中的应用程序。Docker引擎利用容器来运行、隔离各个应用。容器是镜像创建的应用实例，可以创建、启动、停止、删除容器，各个容器之间是是相互隔离的，互不影响。注意：镜像本身是只读的，容器从镜像启动时，Docker在镜像的上层创建一个可写层，镜像本身不变。

**仓库（Repository）**：类似于代码仓库，这里是镜像仓库，是Docker用来集中存放镜像文件的地方。注意与注册服务器（Registry）的区别：注册服务器是存放仓库的地方，一般会有多个仓库；而仓库是存放镜像的地方，一般每个仓库存放一类镜像，每个镜像利用tag进行区分，比如Ubuntu仓库存放有多个版本（12.04、14.04等）的Ubuntu镜像。

* * *
#### 第三个问题：怎么用docker
Docker 常用命令确认容器有在运行，可以通过 docker ps 来查看
使用 docker stop 容器Name 命令来停止容器
查看docker信息 docker info
删除镜像：docker rmi imageID
停用镜像：docker stop ImageID
重启镜像： docker start imageID
删除容器: docker rm ID
docker inspect 来查看 Docker 的底层信息
docker images 查看docker 镜像
从 Docker Hub 网站来搜索镜像，Docker Hub 网址为： https://hub.docker.com/
使用 docker search 命令来搜索镜像。如搜索httpd的镜像。
使用命令 docker pull 来下载镜像。
命令 docker build ， 从零开始来创建一个新的镜像
容器连接：指定容器绑定的网络地址，比如绑定 127.0.0.1。


* * *

#### 第四个问题：怎么安装docker（linux）

###### 1：禁用selinux（特别重要，不然可能pull失败）
禁用selinux，因为selinux和LXC有冲突，故而需要禁用
```
vim /etc/selinux/config
```
以下是/etc/selinux/config的内容
enforcing - SELinux security policy is enforced.
permissive - SELinux prints warnings instead of enforcing.
disabled - No SELinux policy is loaded.SELINUX=disabled  
将SELINUX设为disabled，注意修改后最好重启下机器。

###### 2：安装yum 源
直接使用命令: yum install docker-io  
会报错:No package docker-io available.
![](/assets/blogImg/201812251444200.png) 

 需要先安装yum 源.yum -y install http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
 
######  3：安装docker
 执行：yum install docker-io
 docker就安装成功了
 将docker开机启动：chkconfig docker on
 启动docker：service docker start
 查看docker版本：docker version
 查看docker日志：cat /var/log/docker
![](/assets/blogImg/2019-8-22-2.png) 
 到这里，docker就安装完成了。
######  4.卸载docker（不需要卸载的就直接看四，加速配置）
 列出你安装过的docker：yum list installed | grep docker

![](/assets/blogImg/2019-8-22-3.png) 
 
 删除软件包：yum -y remove docker-io.x86_64
 删除镜像/容器等：rm -rf /var/lib/docker

######  5.docker加速配置，国外的网可能比较慢，配置国内阿里云源（docker1.7修改方法）
 
 执行编辑命令：`vi /etc/sysconfig/docker`
 
 加入下面两行
 other_args=&quot;--registry-mirror=https://wbtwgo1e.mirror.aliyuncs.com&quot;  
 OPTIONS='--registry-mirror=https://wbtwgo1e.mirror.aliyuncs.com' 
 即可

#### 第五个问题：将python打包到docker上

步骤如下：
###### 1：创建一个上传到Linux系统文件夹（文件名任意）
docker_EF_NFCS是要上传到Linux系统，生成Docker镜像的文件夹
![](/assets/blogImg/2019-8-22-4.png) 

 docker_EF_NFCS文件夹下面有两个东东，
 
 一个Dockerfile文件（没有后缀名）
 
 Dockerfile 是一个文本文件，其内包含了一条条的指令(Instruction)，每一条指令构建一层，因此每一条指令的内容，就是描述该层应当如何构建。有了 Dockerfile，就可以创建我们所需要的镜像
 Dockerfile文件内容如下：
 ![](/assets/blogImg/2019-8-22-5.png) 
 
 #基于的基础镜像
 FROM python:3.7
 
 #代码添加到code文件夹
 ADD ./EF_NFCS /code
 
 #设置code文件夹是工作目录
 WORKDIR /code
 
 #安装支持
 RUN pip install -r requirements.txt
 CMD [&quot;python&quot;, &quot;/code/package/server.py&quot;]

* * *

 最后一条命令，就是说，当启动容器时候，执行server.py程序。 
<u> code目录与requirements.txt 同级目录</u>

 一个EF_NFCS（任意起的名字）文件夹：包含工程的代码，和运行工程所需要的环境列表
 
 所以，EF_NFCS文件夹，有两个成员package和requirements.txt
 
![](/assets/blogImg/2019-8-22-6.png) 
 
 package就是Python项目的package（整个源码所在目录）直接复制过来的
 
 requirements.txt生成方法：
 cmd命令，进入到【D:\python\EF_NFCS】目录，也就是工程目录，package的同级目录，
 执行命令：
 pip freeze &gt; 

 就在该目录下生成了一个requirements.txt文件：
 如下图，可能很大，因为没有配合virtualenv ，就把整个环境中的包都列出来，没关系，我们可以删除我们此工程中，不需要的组件
 
![](/assets/blogImg/2019-8-22-7.png) 
 
######   2：上传到linux环境，生成镜像
  2.1：我们将docker_EF_NFCS文件夹，压缩成zip包，上传到Linux虚拟机系统中的某个路径下面
  
![](/assets/blogImg/2019-8-22-8.png) 
  
  2.2：解压：执行命令：
  
  unzip docker_EF_NFCS.zip
  
  2.3：进入docker_EF_NFCS文件夹：
  
![](/assets/blogImg/2019-8-22-9.png) 
  
  2.4：创建镜像：执行命令：
  docker build -t ef_nfcs_img .  
  ef_nfcs_img 为创建的镜像名称，. 表示当前目录（docker_EF_NFCS）
![](/assets/blogImg/2019-8-22-10.png) 
  查看镜像是否创建成功：执行命令：docker images
  
![](/assets/blogImg/2019-8-22-11.png) 
  
  可以看到ef_nfcs_img的镜像，已经存在了 
######   3：运行镜像，生成并启动容器
  执行命令：docker run -it -p 9060:9060 --name ef_nfcs ef_nfcs_img
  
  -it：表示交互式终端的容器，非启动后立刻结束的容器
  
  -p 9060:9060：表示将docker的9060端口，映射到Linux虚拟机的9060端口                        也就是说，访问Linux虚拟机的9060端口，就是在访问docker容器的9060端口
  
  --name ef_nfcs：给容器取个名字，嫌麻烦可以省去
  
  ef_nfcs_img：容器是用哪个镜像启动的（一个容器，必须依赖一个镜像启动）
  
![](/assets/blogImg/2019-8-22-12.png) 
  
  和原来用PyCharm启动的效果一致：
![](/assets/blogImg/2019-8-22-13.png) 
   原来访问系统：
  
![](/assets/blogImg/2019-8-22-14.png) 

* * *
#### 第六个问题：docker运行项目的相关操作

一些实用命令
退出容器又不关闭容器：Ctrl + P + Q
比如这种情况：
![](/assets/blogImg/2019-8-22-15.png) 
直接输入exit 会退出且关闭容器，工程就stop了
或者Ctrl + C，会报错KeyboardInterrupt，工程就也stop了
再访问就访问不了了：
![](/assets/blogImg/2019-8-22-16.png) 
此时，就需要使用命令：docker start 容器ID，去再启动容器后，就可以继续访问工程了，
使用命令：
docker exec -it 容器ID /bin/bash
或者
docker attach  容器ID，重新进入容器后，可查看程序打印的日志
 但是，如果使用Ctrl + P + Q退出容器，就不会中断工程，等于退出容器后，还可访问容器的工程，
 再进入，也是使用命令：docker attach  容器ID
 
 ctrl+d 退出容器且关闭, docker ps 查看无
 
 ctrl+p+q 退出容器但不关闭
 
 查看安装的docker：yum list installed | grep docker
 查看容器：docker ps -a
 查看镜像：docker images
 删除容器：docker rm 容器ID
 删除镜像：docker rmi 镜像ID/镜像名
 安装rz上传，sz下载的命令：yum -y install lrzsz
 docker inspect 容器ID：返回一个Json文件记录Docker容器的配置和状态信息 
 
 将容器导出到容器的镜像文件 ： docker export 容器ID &gt; update.tar
 用容器的镜像文件创建一个新静像：docker import - 镜像名 &lt; update.tar  
 创建完成之后，生成并启动镜像的容器：docker run -it  镜像名
 不行就在后面加一个/bin/bash ,然后进入code的package目录，手动启动程序：python server.py
 比如报错：Error response from daemon: No command specified
![](/assets/blogImg/2019-8-22-17.png) 
  镜像保存为镜像文件： docker save -o update1.tar update（update是一个已经存在的镜像）
  从镜像文件加载生成新镜像：docker load &lt; update1.tar
  
####   第七个问题：生成镜像报错后的none镜像怎么删除
  
  删除none的镜像，要先删除镜像中的容器。要删除镜像中的容器，必须先停止容器。
  
```
$ docker images
  $ docker rmi $(docker images | grep &quot;none&quot; | awk '{print $3}')
```
  直接删除带none的镜像，直接报错了。提示先停止容器。
 
```
 $ docker stop $(docker ps -a | grep &quot;Exited&quot; | awk '{print $1 }') //停止容器$ docker rm 
  $(docker ps -a | grep &quot;Exited&quot; | awk '{print $1 }') //删除容器
  $ docker rmi $(docker images | grep &quot;none&quot; | awk '{print $3}') //删除镜像

```