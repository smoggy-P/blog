---
title: "使用Docker配置ROS noetic环境"
tags: [ROS, Chinese]
categories: ROS学习
math: false
date: 2022-05-15 23:08:35
index_img: /img/ros_docker/index.png
post:
  meta:
    author:  # 作者，优先根据 front-matter 里 author 字段，其次是 hexo 配置中 author 值
      enable: false
    date:  # 文章日期，优先根据 front-matter 里 date 字段，其次是 md 文件日期
      enable: true
      format: "dddd, MMMM Do YYYY, h:mm a"  # 格式参照 ISO-8601 日期格式化
    wordcount:  # 字数统计
      enable: true
      format: "{} 字"  # 显示的文本，{}是数字的占位符（必须包含)，下同
    min2read:  # 阅读时间
      enable: true
      format: "{} 分钟"
    views:  # 阅读次数
      enable: true
      source: "busuanzi"  # 统计数据来源，可选：leancloud | busuanzi   注意不蒜子会间歇抽风
      format: "{} 次"
---

# 1. 提要
最近需要将一段感知代码移植到实验室的pipeline上进行测试，然而之前一直在使用ROS_melodic，原始感知代码目前只在melodic上测试成功，为了实验室其他用noetic的老哥们能测试，就想搭建一个noetic的docker来尝试一下同样的代码能否在noetic上编译成功。（借机学习一下docker）

# 2. 安装过程
## 2.1 安装Docker
这一步基本参考[Docker-从入门到实践](https://yeasy.gitbook.io/docker_practice/install/ubuntu)[^1]的教程，最后在测试安装是否成功时遇到了permission相关的问题，最终通过下面这行代码扩充权限解决：
```sh
sudo chmod 666 /var/run/docker.sock
```
## 2.2 安装ROS镜像
这一步参考[ROS官方网站](http://wiki.ros.org/docker/Tutorials/Docker)[^2]提出的教程，其中部分步骤有差异，因此这里完整记录一下。

首先下载ROS container镜像：
```sh
docker pull ros
```
之后下载noetic所需要的最基本的环境镜像：
```sh
docker pull ros:noetic
## 如果需要下载noetic全部功能:
# docker pull osrf/ros:noetic-desktop-full
```
最后一步启动镜像（与官网描述有所不同）：
```sh
docker run -it ros:noetic
```
在启动的bash中尝试启动`roscore`，如果成功就表示配置完成啦！

# 3. 基本操作
首先引入一下docker的基本概念，主要是**镜像(image)、容器(container)** 的概念。这里可以将镜像理解为c++中定义的类的概念，而容器是该类实例化之后的对象。在上一章中所提到的`docker run`的操作就可以理解为根据提供的镜像创建一个容器。运行选项`-it`的含义如下：

> -t 选项让Docker分配一个伪终端（pseudo-tty）并绑定到容器的标准输入上， -i 则让容器的标准输入保持打开。

通过vscode插件，我们可以很方便地对镜像和容器进行管理。其操作界面如下图：
<p style="text-align: center;">
    <img src="/blog/img/ros_docker/1.png" width=300>
</p>
在container中可以很方便地进行运行和终止容器的操作，也可以删除不需要的容器。最右边的灰字为容器的名称（返回到之前提到c++中类的类比，这里的名字可以理解为变量名），通过下面的files可以对容器中的文件进行管理。

后续有时间会更新一些dockerfile的使用，目前只手动在docker中安装了`git`以及`catkin tool`，似乎这些步骤都能直接写进dockerfile中。

# 参考
[^1]: “Docker-从入门到实践” https://yeasy.gitbook.io/docker_practice/ (accessed May 12, 2022).

[^2]: “docker/Tutorials/Docker - ROS Wiki.” http://wiki.ros.org/docker/Tutorials/Docker (accessed May 12, 2022).

