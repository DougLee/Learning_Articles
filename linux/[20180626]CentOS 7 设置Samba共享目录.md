

# CentOS 7设置Samba共享目录

## 1. 安装Samba服务

```bash
yum -y install samba
# 查看yum源中Samba版本
yum list | grep samba
# 查看samba的安装情况
rpm -qa | grep samba
```

Samba服务器安装完之后, 会生成配置文件目录/etc/samba, /etc/samba/smb.conf是samba的核心配置文件.

## 2. 启动Samba服务

Samba服务安装完成之后有两种方法启动:

```bash
service smb start/stop/restart/status
# 或者
systemctl start/stop/restart/status smb.service

# 设置smb服务开机启动
systemctl enable smb.service
```

## 3. 开放Samba服务使用到的端口号

Samba服务会用到如下的一些端口号:

* 137(UDP): NetBIOS名字服务
* 138(UDP): NetBIOS数据报服务
* 139(TCP):  文件和打印共享
* 389(TCP): 用于LDAP
* 445(TCP): NetBIOS服务在windows 2000及以后使用此端口
* 901(TCP): 用于SWAT, 网页管理Samba

如果不想关闭防火墙的话, 就要在CentOS中放开Samba使用到的TCP端口号

```bash
firewall-cmd --zone=public -add-port=139/tcp --permanent
firewall-cmd --zone=public -add-port=389/tcp --permanent
firewall-cmd --zone=public -add-port=445/tcp --permanent
firewall-cmd --zone=public -add-port=901/tcp --permanent

firewall-cmd --reload

# 查看已经放开的端口号
firewall-cmd --list-all
```

## 4. 配置Samba服务

### 配置匿名访问, 任何人都可以访问的共享目录

1. 创建共享目录

```bash
mkdir /opt/shares

# 因为需要设置匿名用户可以上传下载文件, 所以需要给shares目录授予nobody权限
chown -R nobody:nobody /opt/shares
```

2. 修改/etc/samba/smb.conf文件

```bash
cp /etc/samba/smb.conf /etc/samba/smb.conf.bak
vi /etc/samba/smb.conf
```

修改配置如下:

```conf
# See smb.conf.example for a more detailed config file or
# read the smb.conf manpage.
# Run 'testparm' to verify the config is correct after
# you modified it.

[global]
        workgroup = SAMBA
        security = user
        map to guest = Bad User
        log file = /var/log/samba/log.%m


[public]
        comment = Public Stuff
        path = /opt/shares
        public = yes
        read only = No

```

其中 path就是上边设置的共享目录, read only 表示是否有写权限

3. 修改完配置文件之后重启samba服务

```bash
systemctl restart smb.service
```

4. 测试smb.conf配置是否正确

```bash
# 使用testparm命令
testparm
```

5. 至此就配置完成, 可以从Windows下访问samba的共享目录.



### 配置指定用户可以访问的共享目录

设置共享目录, 只允许指定用户组的用户访问

1. 添加工作组cnki和用户share

```bash
groupadd cnki
# useradd -g 组名 用户名
useradd -g cnki share
# 设置用户share的密码
passwd share

#删除用户
userdel -r 用户名
```

2. 把要访问的账户添加到samba的账户中

光添加系统账户还不够, 需要把已经存在的系统账户添加到samba中才可以访问共享目录

```bash
# smbpasswd 参数: -a: 添加 -x: 删除 -d: 禁用 -e: 启用
smbpasswd -a share
```

3. 创建共享目录

```bash
mkdir /opt/shares1

# chown -R 用户名:组名 目录
chown -R share:cnki /opt/shares1
```

4. 设置samba服务

修改配置文件/etc/samba/smb.conf如下

```conf
# See smb.conf.example for a more detailed config file or
# read the smb.conf manpage.
# Run 'testparm' to verify the config is correct after
# you modified it.

[global]
        workgroup = SAMBA
        security = user
        map to guest = Bad User
        log file = /var/log/samba/log.%m

[shares]
        comment = CNKI
        path = /opt/shares1
        # 表示用户组
        valid users = @cnki
        read only = No

```

5. 重启smb服务

```bash
systemctl restart smb.service
# 检查smb.conf文件是否配置正确
testparm
```

6. 至此配置完成, 可以在Windows平台下通过用户名share/share来访问共享目录了.