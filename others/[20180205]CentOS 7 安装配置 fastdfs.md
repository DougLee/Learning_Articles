## 单机环境部署

### 安装必要的工具

```bash
yum install wget
yum install gcc gcc-c++
yum install perl
yum install unzip

# nginx 需要的包
yum install -y pcre pcre-devel
yum install -y zlib zlib-devel
```



### 下载安装libfastcommon和FastDFS

```bash
mkdir /data && cd /data

wget https://github.com/happyfish100/fastdfs/archive/V5.11.tar.gz
wget https://github.com/happyfish100/libfastcommon/archive/V1.0.36.tar.gz
```

安装libfastcommon

```bash
tar -zxvf V1.0.36.tar.gz
cd libfastcommon-1.0.36
./make.sh 
./make.sh install
```

安装fastdfs

```bash
cd /data
tar -zxvf V5.11.tar.gz && cd fastdfs-5.11
./make.sh && .make.sh install
```



### 配置Tracker服务器

在`/etc/fdfs`路径下找到tracker.conf.sample, 复制该文件

```bash
cp tracker.conf.sample tracker.conf
```

然后修改*tracker.conf*文件中的base_path和http.server两个参数

其中, base_path中的路径需要在启动前创建

```xml
base_path=/data/fastdfs
http.server_port=80
```

配置修改完成之后可以使用下边的命令启动Tracker服务

```bash
/usr/bin/fdfs_trackerd /etc/fdfs/tracker.conf start/stop/restart
```

或者

```bash
service trackerd start/stop/restart
```

启动完成之后如果没有报错可以查看下端口监听来判断Tracker服务是否正确启动

```bash
netstat -unltp | grep fdfs
```

### 配置Storage服务器

同样在`/etc/fdfs`路径下找到storage.conf.sample文件, 复制该文件

```bash
cd /etc/fdfs
cp storage.conf.sample storage.conf
vi storage.conf
```

修改base_path store_path0和tracker_server三个配置, 其中路径需要提前建好

```xml
base_path=/data/fastdfs/storage
store_path0=/data/fastdfs/storage
tracker_server=192.168.107.241:22122
```

修改配置完成后用如下命令启动Storage服务器

```bash
/usr/bin/fdfs_storaged /etc/fdfs/storage.conf
# 或者
service fdfs_storaged start
```

启动完成之后依然使用

```bash
netstat -antlp | grep fdfs
```

来查看storage服务器是否启动成功.

###  配置Nginx 及Nginx插件

1. 下载nginx和插件fastdfs-nginx-module模块

```bash
wget -c https://nginx.org/download/nginx-1.10.1.tar.gz 
wget https://github.com/happyfish100/fastdfs-nginx-module/archive/master.zip
```

2. 解压安装包,  其中zip文件需要安装unzip工具来解压

```bash
unzip master.zip
tar -zxvf nginx-1.10.1.tar.gz
```

3. 安装nginx及其插件

```bash
cd nginx-1.10.1
./configure --add-module=../fastdfs-nginx-module-master/src
make && make install
```

4. 配置nginx插件 fastdfs-nginx-module


```bash
cd fastdfs-nginx-module/src
cp mod_fastdfs.conf /etc/fdfs
vi /etc/fdfs/mod_fastdfs.conf
```

修改tracker_server  url_have_group_name和store_path0三个参数

```xml
tracker_server=192.168.107.241:22122
url_have_group_name=true
store_path0=/data/fastdfs/storage
```

然后从fastdfs的安装包路径下copy三个文件到/etc/fdfs

```bash
cd /data/fastdfs-5.11/conf
cp anti-steal.jpg /etc/fdfs
cp http.conf /etc/fdfs
cp mime.types /etc/fdfs
```



5. 配置nginx

在`nginx.conf`加入

```bash
cd /usr/local/nginx/conf
vim nginx.conf
```

```xml
location /group1/M00 { 
    root /data/fastdfs/storage/; 
    ngx_fastdfs_module; 
}
```

因为配置了group1/M00的访问, 所以需要建立一个group1文件夹, 并建立M00到data的软连接

```bash
mkdir /data/fastdfs/storage/data/group1
ln -s /data/fastdfs/storage/data/ /data/fastdfs/storage/data/group1/M00

```

启动nginx

```bash
/usr/local/nginx/sbin/nginx
/usr/local/nginx/sbin/nginx -s reload     # 重启nginx
```

### 上传文件测试

1. 启动Tracker和Storage服务器

```bash
service fdfs_trackerd start
service fdfs_storaged start
```

2. 启动nginx

```bash
# 启动nginx
/usr/local/nginx/sbin/nginx
# 重启nginx
/usr/local/nginx/sbin/nginx -s reload
# nginx端口默认80, 查看命令
netstat -anp | grep 80

# 添加放开端口设置
firewall-cmd --permanent --zone=public --add-port=80/tcp
systemctl restart firewalld
```

3. 配置client.conf

```bash
cd /etc/fdfs/
cp client.conf.sample client.conf
vi client.conf
```

修改base_path和tracker_server的两个配置

```xml
base_path=/data/fastdfs/
tracker_server=192.168.107.241:22122
```

4. 上传文件

```bash
fdfs_test /etc/fdfs/client.conf upload filename
```

上传成功后会返回一个文件的路径.

## 集群环境部署

### Tracker 服务器

需要安装FastDFS和Nginx, 这里的nginx是为了对外提供文件下载接口, 起到负载均衡的作用. tracker的nginx代理会转发至storage上的nginx

1. 安装FastDFS
   同单机版
2. 配置Tracker服务器
   同单机版
3. 安装Nginx
   不需配置nginx插件, 其余同单机版

```bash
firewall-cmd --permanent --zone=public --add-port=80/tcp
firewall-cmd --permanent --zone=public --add-port=22122/tcp
systemctl restart firewalld
```



### Storage服务器

需要安装FastDFS和Nginx, 这里的nginx和tracker上的nginx有区别, storage上的nginx需要安装FastDFS-nginx模块, 此模块的作用是将FastDFS和nginx进行整合, nginx对外提供http文件下载接口

1. 安装FastDFS
   同单机版
2. 配置Storage服务器
   同单机版
3. 安装Nginx
   同单机版

```bash
firewall-cmd --permanent --zone=public --add-port=80/tcp
firewall-cmd --permanent --zone=public --add-port=23000/tcp
systemctl restart firewalld
```





## firewall-cmd放开端口

```bash
# 查看防火墙的状态
systemctl status firewalld
# 关闭防火墙
systemctl stop firewalld
# 安装firewalld
yum install firewalld
```

### firewall-cmd 添加端口过滤

```bash
# 1. 添加放开端口的规则
firewall-cmd --permanent --zone=public --add-port=80/tcp
# 2. 重启firewalld使规则生效
systemctl restart firewalld
# 3. 检查更改是否生效
firewall-cmd --zone=public --query-port=80/tcp
```



