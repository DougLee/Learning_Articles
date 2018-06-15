## 在CentOS 7 中卸载FastDFS

### 1.  停止tracker和storage 删除 fdfs_trackerd 和fdfs_storaged

```bash
service fdfs_trackerd stop
service fdfs_storaged stop


rm -rf /etc/init.d/fdfs_*
```

### 2. 通过storage.conf中找到base_path和store_path然后删除

```bash
cat /etc/fdfs/storage.conf | grep base_path
cat /etc/fdfs/storage.conf | grep store_path
```

### 3. 通过 tracker.conf找到base_path然后删除

```bash
cat /etc/fdfs/tracker.conf | grep base_path
rm -rf "base_path的路径"
```

### 4. 删除配置文件目录

```bash
rm -rf /etc/fdfs
```

### 5. 删除/usr/bin 目录下FastDFS的可执行文件

```bash
ll /usr/bin/fdfs_*

rm -rf /usr/bin/fdfs_*
```

### 6. 删除/usr/include/目录下FastDFS相关的shell脚本

```bash
ll /usr/include/fastdfs/*
rm -rf /usr/include/fastdfs/
```

### 7. 删除/usr/lib64目录下的libfdfsclient*库文件

```bash
ll /usr/lib64/libfdfsclient*
rm -rf /usr/lib64/libfdfsclient*
```

### 8. 删除/usr/lib目录下的libfdfsclient*库文件

```bash
ll /usr/lib/libfdfsclient*
rm -rf /usr/lib/libfdfsclient*
```

至此, FastDFS就完全的卸载干净了.