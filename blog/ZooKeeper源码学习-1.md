title: "ZooKeeper源码学习(1) Eclipse编译ZooKeeper源码"
date: 2015-12-29 15:22:53
tags:
- Zookeeper
- Java
---

接下来的工作可能要用到zookeeper，学习一下zookeeper的源码，是为记录。
这是第一篇，在Eclipse中编译zookeeper源码
<!--more-->
## 安装Ant

1. 下载Ant， 地址：[http://ant.apache.org/bindownload.cgi](http://ant.apache.org/bindownload.cgi)
2. 解压到指定目录，并设置ANT_HOME(解压的路径)和PATH(%ANT_HOME%\bin)
3. 打开cmd，输入ant -version，出现版本信息说明安装Ant成功

## 用ant编译Zookeeper源码

ZooKeeper代码[下载地址](https://github.com/apache/zookeeper)。

解压代码，切换到ZooKeeper源码根目录下面，输入命令：ant eclipse编译。

编译之前可以改一下ivysettings.xml中的maven镜像路径，改为国内的镜像地址如：http://maven.oschina.net/content/groups/public/，加快下编译速度，如下图
![](/image/zookeeper1/1.jpg)

## 导入编译后的ZooKeeper工程

ant编译完成之后，打开Eclipse，选择import，类型是现有的Eclipse工程。
![](/image/zookeeper1/2.png)

点击完成，发现源码有几处错误
![](/image/zookeeper1/3.jpg)

该错误跟JDK版本有关，需要jdk6，

在导入的ZooKeeper工程右键=>properties=>Java Compiler,将从名牌里尔 compliance level 切换到1.6：
![](/image/zookeeper1/4.jpg)

这样代码上的错误消失。可以开始学习ZooKeeper源码了。