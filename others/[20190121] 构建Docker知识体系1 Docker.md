# 构建Docker知识体系 - Docker基础

## Docker是什么

基于Go语言实现的一种容器技术.

#### 为什么出现

环境不一致, 开发和运维之间的鸿沟

#### 解决了什么问题

解决了运行环境和配置问题, 便于持续集成, 有助于整体发布

#### Docker能干什么

  

### Docker的应用场景

http://dockone.io/article/126

### 容器和虚拟机的区别

虚拟化技术就是虚拟了整套环境, 缺点: 资源占用多, 启动慢



### Docker的体系架构

## 常用命令

### 帮助命令

1. docker info
2. docker version
3. docker --help

### 镜像命令

Docker名称=name+tag

1. docker images
2. docker search
3. docker pull
4. docker rmi 镜像id
   删除单个: docker rmi -f 镜像名
   删除多个: docker rmi 镜像名1 镜像名2
   删除全部: docker rmi -f $(docker images -q)

###容器命令





