title: "Hadoop1.2.1单机版配置说明"
date: 2015-07-29 21:46:35
tags:
---

前端时间一直在研究Nutch，需要对Hadoop有所了解。之前没有接触过Hadoop，在这里把学习Hadoop的过程记录下来。

今天第一篇，Hadoop1.2.1单机版配置说明。


环境：Ubuntu14.04

1. 前提：JDK安装并设置。
2. 下载hadoop.1.2.1.tar.gz。
3. 例如在usr/locarl/下新建一个hadoop文件夹，将下载的hadoop放到这个文件夹下。
4. 解压hadoop-1.2.1.tar.gz，命令 sudo tar -zxf hadoop-1.2.1.tar.gz。
5. 修改hadoop环境变量：
    a. 取得hadoop-1.2.1文件夹的读写权限：Sudo chown -R douglee hadoop-1.2.1
    b. 修改hadoop-env.sh文件：sudo gedit hadoop-1.2.1/conf/hadoop-env.sh
    c. 修改JAVA_HOME为本机的jdk安装地址即可
6. 验证hadoop是否正常运行： 
    a. cd hadoop-1.2.1
    b. Bin/hadoop version， 是否出来hadoop的相关版本信息
7. 运行wordcount实例程序
    a. 新建一个input文件夹：Mkdir input
    b. 将conf下的文件复制到input下： cp conf/* input
    c. 运行wordcount程序，将结果写入output：bin/hadoop jar hadoop-example-1.2.1.jar wordcount input output 
    d. Cat output/*  查看输出结果
8. 参考：http://blog.csdn.net/wyswlp/article/details/10564847

下一篇记录一下伪分布式的配置说明。
