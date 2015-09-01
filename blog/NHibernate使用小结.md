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

######1. 新建项目，添加NHibernate引用

引用从官网下载的dll文件，或者在VS中可以使用Nuget下载安装NHibernate，安装完之后会有两个引用：NHibernate.dll和IesiCollections.dll。这里以Nuget安装为例。

#####2. 新建数据库及使用的表

实例使用一个简单的数据库school，有两个表，Class和Student
![pic](/image/NHibernate使用小结/db.jpg)

#####3. 添加NHibernate相关的配置文件

项目中需要添加一个hibernate.cfg.xml配置文件，文件中包含数据库的驱动信息，数据库的链接字符串等信息。

```xml

<?xml version="1.0" encoding="utf-8"?>
<!-- 
This template was written to work with NHibernate.Test.
Copy the template to your NHibernate.Test project folder and rename it in hibernate.cfg.xml and change it 
for your own use before compile tests in VisualStudio.
-->
<!-- This is the ByteFX.Data.dll provider for MySql -->
<hibernate-configuration  xmlns="urn:nhibernate-configuration-2.2" >
    <session-factory name="NHibernate.Test">
        <property name="connection.driver_class">NHibernate.Driver.MySqlDataDriver</property>
        <property name="connection.connection_string">
      Database=school;Data Source=127.0.0.1;User Id='cnki';Password='';charset='utf8';pooling=true;Convert Zero Datetime=True; Allow Zero Datetime=True
    </property>
        <property name="dialect">NHibernate.Dialect.MySQLDialect</property>
    </session-factory>
</hibernate-configuration>

```
在项目根目录下的package/NHibernate.x.xxx/ConfigurationTemplates文件夹下已经有了对应各个数据库的配置模板，如MSServer、Mysql、Oracle等。

需要注意的是，这个xml配置文件需要设置下“如果较新则复制”。

#####4. 编写Model以及对应的Mapping文件

编写Grade和Student两个model，以及对应的Mapping文件。


```csharp

    public class Grade
    {
        public virtual int ID { get; set; }

        public virtual string Name { get; set; }
    }

    public class Student
    {
        public virtual int ID { get; set; }

        public virtual string Name { get; set; }

        public virtual Grade Grade { get; set; }
    }

```

Mapping文件

```xml

<?xml version="1.0" encoding="utf-8" ?>
<hibernate-mapping xmlns="urn:nhibernate-mapping-2.2" assembly="NHibernate.Demo" namespace="NHibernate.Demo.Models">

  <class name="Grade" table="Class">
    <id name="ID" type="Int32"  column ="id" access="property">
    </id>

    <property name ="Name" column="Name"/>
  </class>
</hibernate-mapping>

<?xml version="1.0" encoding="utf-8" ?>
<hibernate-mapping xmlns="urn:nhibernate-mapping-2.2" assembly="NHibernate.Demo" namespace="NHibernate.Demo.Models">

  <class name="Student" table="Student">
    <id name="ID" type="Int32"  column ="id" access="property">
    </id>

    <property name ="Name" column="Name" />
    <one-to-one name="Grade" class="Grade" constrained="true"/>

  </class>
</hibernate-mapping>

```
这里需要注意在hibernate.cfg.xml文件中添加model和Mapping文件的引用。

```xml

<mapping assembly="NHibernate.Demo"/>

```

#####5. 编写业务逻辑代码CRUD

```csharp

public class GradeService
    {
        #region NHibernate 封装

        private static readonly object lockObj = new object();

        protected static ISessionFactory factory = null;

        public static ISessionFactory Factory
        {
            get
            {
                if (factory == null)
                {
                    lock (lockObj)
                    {
                        if (factory == null)
                        {
                            var cfg = new Configuration();
                            factory = cfg.Configure().BuildSessionFactory();
                        }
                    }
                }
                return factory;
            }
        }

        public ISession GetCurrentSession()
        {
            return Factory.GetCurrentSession();
        }

        public ISession OpenSession()
        {
            return Factory.OpenSession();
        }

        #endregion NHibernate 封装

        protected ISession sessionEX = null;

        public GradeService()
        {
            sessionEX = this.OpenSession();
        }

        public List<Grade> Finds()
        {

            var criteria = sessionEX.CreateCriteria(typeof(Grade));
            var result1 = criteria.List<Grade>();

            //return result1.ToList();

            string sql = "select * from class";

            var query = sessionEX.CreateSQLQuery(sql).AddEntity(typeof(Grade));

            var result = query.List<Grade>().ToList();

            return result;
        }
    }

```

#####6. 
