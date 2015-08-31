title: "NHibernate使用小结"
date: 2015-08-31 14:27:06
tags:
---

### NHibernate简单介绍
NHibernate是一个面向.Net环境的对象/关系数据库映射工具。主要用来把对象模型表示的对象映射到基于SQL的关系模型数据结构中去。NHibernate是基于.Net的Hibernate实现。

NHibernate不仅仅管理.Net类到数据库表的映射，还提供数据查询和获取的方法。NHibernate的目标是解放开发者通常的数据持久化相关的编程任务。

### NHibernate下载
初次使用NHibernate，可以先到[这里](http://sourceforge.net/projects/nhibernate/)下载最新的NHibernate版本。如果使用VS的话，可以在Nuget中搜索NHibernate下载安装。

### NHibernate初次使用

1. 新建项目，添加NHibernate引用

2. 新建数据库及使用的表

3. 添加NHibernate相关的配置文件

4. 编写Model以及对应的Mapping文件

5. 编写业务逻辑代码CRUD
