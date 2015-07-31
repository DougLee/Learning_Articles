title: "Hadoop1.2.1伪分布式配置说明"
date: 2015-07-30 13:58:37
tags:
---
这篇文章记录一下伪分布式的配置。伪分布式较之单机版多了几处配置。环境依然是Ubuntu14.04

1. 前提条件：
    a. JDK安装并配置
    b. 安装有ssh
        1). sudo apt-get install openssh-server
        2). ssh-kengen -t dsa -P '' -f ~/.ssh/id_dsa
        3). cat ~/.ssh/id_dsa.pub >> ~/.ssh/authorized_keys
        4). ssh localhost
2. 在hadoop根目录下创建几个目录
    a. mkdir tmp
    b. mkdir hdfs
    c. mkdir hdfs/name
    d. mkdir hdfs/data
3. 修改配置文件，需要修改conf/文件夹下的core-site.xml hdfs-site.xml和mapred-site.xml三个配置文件
    a. core-site.xml
      ![core-site.xml](/image/Hadoop1-2-1伪分布式配置说明/core-site.png)
    b. hdfs-site.xml
      ![hdfs-site.xml](/image/Hadoop1-2-1伪分布式配置说明/hdfs-site.png)
    c. mapred-site.xml
      ![mapred-site.xml](/image/Hadoop1-2-1伪分布式配置说明/mapred-site.png)
4. 格式化hdfs： hadoop namenode -format
5. data文件夹权限设置： sudo chmod 755 /hdfs/data
6. 启动hadoop: start-all.sh
7. jps命令，查看hadoop是否启动成功，如果有6个进程则说明成功
  ![hadoop status](/image/Hadoop1-2-1伪分布式配置说明/status.png)
8. 检查运行状态：
    a. http://localhost:50030  -Hadoop管理界面
    b. http://localhost:50060  -Hadoop TaskTracker状态
    c. http://localhost:50070  -Hadoop DFS状态
9. 至此hadoop已经配置完成，接下来运行wordcount任务体验一把
    a. 在dfs中创建input文件夹：hadoop dfs -mkdir input
    b. 将conf中的文件拷贝到dfs中的input：hadoop dfs -copyFromLocal conf/* input
    c. 运行job：hadoop jar hadoop-example-1.2.1 jar wordcount input output 
    d. 查看输出信息： hadoop dfs -cat output/*
10. 参考
    http://blog.csdn.net/devtao/article/details/16105579
