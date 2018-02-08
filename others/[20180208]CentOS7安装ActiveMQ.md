## CentOS 7 安装ActiveMQ

ActiveMQ需要JDK环境, 所以安装ActiveMQ首先需要安装JDK.

### 1. 下载ActiveMQ

去[ActiveMQ官网](http://activemq.apache.org/), 下载ActiveMQ的安装包

```bash
wget http://mirror.bit.edu.cn/apache//activemq/5.15.3/apache-activemq-5.15.3-bin.tar.gz
```

### 2. 安装ActiveMQ

```bash
# 准备软件的安装路径
mkdir /usr/local/software

# 解压ActiveMQ到软件安装路径
tar -zxvf apache-activemq-5.15.3-bin.tar.gz -C /usr/local/software
# 方便起见, 把解压后的apache-activemq-* 文件夹 改名为apache-activemq
mv /usr/local/software/apache-activemq-* /usr/local/software/apache-activemq

```

ActiveMQ此时就已经安装成功了.

### 3. 启动ActiveMQ

```bash
cd /usr/local/software/apache-activemq

# 前台进程启动ActiveMQ
./bin/activemq console
# 后台进程启动ActiveMQ
./bin/activemq start

# 停止ActiveMQ
./bin/activemq stop
```

启动成功之后可以通过 `http://ip:8161`访问ActiveMQ管理界面, 默认用户名密码是`admin/admin`

访问之前需要放开8161端口

```bash
firewall-cmd --zone=public --add-port=8161/tcp --permanent
firewall-cmd --reload
```

### 4. 创建ActiveMQ Systemd服务

```bash
vi /etc/systemd/system/activemq.service
```

在activemq.service*文件中写入以下内容

```
[Unit]
Description=ActiveMQ service
After=network.target

[Service]
Type=forking
WorkingDirectory=/opt/activemq/bin
ExecStart=/var/activemq/bin/activemq start
ExecStop=/var/activemq/bin/activemq stop
User=root
Group=root

[Install]
WantedBy=multi-user.target
```

现在可以使用如下命令来操作ActiveMQ

```bash
# 重新加载一下Systemd unit
systemctl daemon-reload
# 然后就可以使用下面的命令操作ActiveMQ了
systemctl start/stop/status activemq
```



### 

