zookeeper 需要用到JDK

## 单机环境安装

### 1. 下载Zookeeper安装包 

[下载地址](http://apache.fayea.com/zookeeper/stable/zookeeper-3.4.10.tar.gz)

### 2. 解压 Zookeeper

```bash
tar -zxvf zookeeper-3.4.10.tar.gz
cd zookeeper-3.4.12/conf
```

### 3. 配置zookeeper

```bash
cd zookeeper-3.4.12/conf
cp zoo_sample.cfg zoo.cfg
vi zoo.cfg
```

修改配置文件的如下配置

```conf
ticketTime=2000
clientPort=2181
dataDir=/opt/zookeeper/data
dataLogDir=/opt/zookeeper/logs
```

### 4. 启动zookeeper

```bash
cd zookeeper-3.4.12/bin
sh zkServer.sh start|stop|restart|status 

# zk客户端连接
sh zkCli.sh -server localhost:  
```

## 集群安装

集群的安装大体和单机环境安装差不多, 主要区别有两处

### 1. 修改zookeeper配置文件

```bash
vi zoo.cfg
```

修改zoo.cfg配置文件如下

```conf
server.1=192.168.199.21:2888:3888
server.2=192.168.199.22:2888:3888
server.3=192.168.199.23:2888:3888
```

然后在数据目录下新建一个myid文件 内容只有对应的数字.



启动





