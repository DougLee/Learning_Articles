

## Kubernetes中的重要概念

### Cluster

### Master

集群控制节点, 主要职责是调度, 决定将应用放在那里运行.

### Node

运行容器应用. 由Master管理.

### Pod

最小部署单元, 可包含多个容器, 是连接在一起的容器组合并共享文件卷. Pod中的容器会作为一个整体呗Master调度到一个Node上运行.

### Controller

Kubernetes中通过Controller来管理Pod. Controller定义了Pod的部署特性. 

为了满足不同的业务场景, Kubernetes提供了多种Controller, 如下

1. Deployment: 最常用的Controller. Deployment可以管理Pod的多个副本, 并确保Pod按照期望的状态运行.
2. ReplicaSet: 实现了Pod的多副本管理.
3. DaemonSet: 用于每个Node最多只运行一个Pod副本的场景.
4. StatefuleSet: 能够保证Pod的每个副本在整个声明周期中名称是不变的, 其他的Controller不能提供这个功能, 同时StatefuleSet会保证副本按照固定的顺序启动,更新或者删除.
5. Job: 用于运行结束就删除的应用.

### Service

Kubernetes Service定义了外界访问一组特定Pod的方式. Service有自己的IP和端口, Service为Pod提供了负载均衡.

Kubernetes运行容器与访问容器这两项任务分别由Controller和Service执行.

### NameSpaces

对一组资源和对象的抽象集合.



## Kubernetes组件



