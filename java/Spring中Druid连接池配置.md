
本文记录了使用Druid的方法, 包括Spring和Spring boot中使用Druid的配置方法. 

## Spring中配置Druid连接池

以链接mysql为例
### 1 添加druid依赖
```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>${version}</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.43</version>
</dependency>
```

###2 在Spring配置中添加Druid相关配置
```xml

<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource" init-method="init" destroy-method="close">
     <!-- 基本属性 url、user、password -->
     <property name="driverClassName" value="${jdbc.driver}" />
     <property name="url" value="${jdbc_url}" />
     <property name="username" value="${jdbc_user}" />
     <property name="password" value="${jdbc_password}" />
     <!-- 配置监控统计拦截的filters -->
     <property name="filters" value="stat" />
     <!-- 配置初始化大小、最小、最大连接池数量 -->
     <property name="maxActive" value="20" />
     <property name="initialSize" value="1" />
     <property name="minIdle" value="1" />
     <!-- 配置获取连接等待超时的时间 -->
     <property name="maxWait" value="60000" /> 
     <!-- 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒 -->
     <property name="timeBetweenEvictionRunsMillis" value="60000" />
     <!-- 配置一个连接在池中最小生存的时间，单位是毫秒 -->
     <property name="minEvictableIdleTimeMillis" value="300000" />
     <property name="testWhileIdle" value="true" />
     <property name="testOnBorrow" value="false" />
     <property name="testOnReturn" value="false" />
     <!-- 打开PSCache，并且指定每个连接上PSCache的大小 -->
     <property name="poolPreparedStatements" value="true" />
     <property name="maxOpenPreparedStatements" value="20" />
</bean>
```

###3 在web.xml中添加Druid相关配置

```xml
  <filter>
    <filter-name>DruidWebStatFilter</filter-name>
    <filter-class>com.alibaba.druid.support.http.WebStatFilter</filter-class>
    <init-param>
      <param-name>exclusions</param-name>
      <param-value>*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*</param-value>
    </init-param>
  </filter>
  <filter-mapping>
    <filter-name>DruidWebStatFilter</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>
```
然后运行程序访问[http://localhost:8080/druid](http://localhost:8080/druid)即可

## Spring boot中配置Druid连接池

首先添加pom依赖, 然后application.properties配置信息如下:

```yml
# 驱动配置信息  
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource  
spring.datasource.url = jdbc:mysql://127.0.0.1:3306/mealsystem?useUnicode=true&characterEncoding=utf-8  
spring.datasource.username = root  
spring.datasource.password = 123456  
spring.datasource.driverClassName = com.mysql.jdbc.Driver  
   
#连接池的配置信息  
spring.datasource.initialSize=5  
spring.datasource.minIdle=5  
spring.datasource.maxActive=20  
spring.datasource.maxWait=60000  
spring.datasource.timeBetweenEvictionRunsMillis=60000  
spring.datasource.minEvictableIdleTimeMillis=300000  
spring.datasource.validationQuery=SELECT 1 FROM DUAL  
spring.datasource.testWhileIdle=true  
spring.datasource.testOnBorrow=false  
spring.datasource.testOnReturn=false  
spring.datasource.poolPreparedStatements=true  
spring.datasource.maxPoolPreparedStatementPerConnectionSize=20  
spring.datasource.filters=stat,wall,log4j  
spring.datasource.connectionProperties=druid.stat.mergeSql=true;druid.stat.slowSqlMillis=5000
```

## Spring boot的Druid监控设置

首先druid配置文件中需要有如下相关filter配置, 内置的Filter名字可参考[这里](https://github.com/alibaba/druid/wiki/%E5%86%85%E7%BD%AEFilter%E7%9A%84%E5%88%AB%E5%90%8D)
```
spring.datasource.filters=stat,wall,log4j 
```
然后再注册Servlet和Filter. 有两种方法
### 方法一 使用代码注册Servlet和Filter

新建一个DruidConfiguration文件

```java

@Configuration
public class DruidConfiguration {

    @Bean
    public ServletRegistrationBean druidServlet() {
        ServletRegistrationBean servletRegistrationBean = new ServletRegistrationBean();
        servletRegistrationBean.setServlet(new StatViewServlet());
        servletRegistrationBean.addUrlMappings("/druid/*");

        Map<String, String> initParameters = new HashMap<String, String>();
        initParameters.put("loginUsername", "admin");
        initParameters.put("loginPassword", "admin");
        initParameters.put("resetEnable", "false");

        servletRegistrationBean.setInitParameters(initParameters);

        return servletRegistrationBean;
    }

    @Bean
    public FilterRegistrationBean filterRegistrationBean() {
        FilterRegistrationBean filterRegistrationBean = new FilterRegistrationBean();
        filterRegistrationBean.setFilter(new WebStatFilter());
        filterRegistrationBean.addUrlPatterns("/*");
        filterRegistrationBean.addInitParameter("exclusions", "*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*");
        return filterRegistrationBean;
    }

}
```
然后运行程序访问[http://localhost:8080/druid](http://localhost:8080/druid)即可

### 方法二 原生的servlet和filter方式

配置Servlet代码
```java
@WebServlet(
        urlPatterns = "/druid/*",
        initParams = {
                @WebInitParam(name = "loginUsername", value = "admin"),
                @WebInitParam(name = "loginPassword", value = "admin"),
                @WebInitParam(name = "resetEnable", value = "admin"),
        }
)
public class DruidStatViewServlet extends StatViewServlet {
}
```

配置filter
```java
@WebFilter(
        filterName = "druidWebStatFilter",
        urlPatterns = "/*",
        initParams = {
                @WebInitParam(name = "exclusions", value = "*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*")
        }
)
public class DruidWebStatFilter extends WebStatFilter {
}
```

然后在spring boot启动类Application.java上加注解@ServletComponentScan 设置Servlet自动扫描.访问[http://localhost:8080/druid](http://localhost:8080/druid)测试.

