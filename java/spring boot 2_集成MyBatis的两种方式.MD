# Spring Boot 2.0整合MyBatis的两种方式

## 准备工作

### 创建项目

使用IDea创建一个Spring Boot项目, 项目结构如图

![img](..\images\spring_boot2\1.png)

### 数据库

创建数据库及数据库表user

```sql
CREATE TABLE `user` (
  `id` int(11) NOT NULL,
  `name` varchar(255) DEFAULT NULL,
  `password` varchar(255) DEFAULT NULL,
  `create_at` datetime DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```



### 配置pom文件的依赖

在pom文件中添加如下依赖

```xml
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.0.0</version>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
```



### 配置application.yml

在application.yml文件中添加数据库的相关配置

```yml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://192.168.13.237/test?serverTimezone=UTC
    username: cnki
    password: cnki
```



### 创建domain层

创建User.class

```java
public class User {
    private int id;
    private String name;
    private String password;
    private Timestamp create_at;

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public Timestamp getCreate_at() {
        return create_at;
    }

    public void setCreate_at(Timestamp create_at) {
        this.create_at = create_at;
    }
}
```



## xml配置的方式使用MyBatis

### Dao层

UserDao.java:

```java
@Mapper
public interface UserDao {
    /**
     * 通过用户名查找用户
     * @param name
     * @return
     */
    User findUserByName(String name);
}
```



### Mapper文件

UserMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.example.demo.dao.UserDao">

    <select id="findUserByName" parameterType="String" resultType="com.example.demo.domain.User">
        SELECT * FROM user WHERE name = #{name}
    </select>
</mapper>
```



### Service层

UserService.java

```java
@Service
public class UserService {

    @Autowired
    private UserDao userDao;

    public User findUserByName(String name){
        return userDao.findUserByName(name);
    }
}
```

### 修改Application.yml配置文件

在配置文件中添加如下一条配置:

```yml
mybatis:
  mapper-locations: classpath:mapper/*.xml
```



### Controller层

UserController.java

```java
@RestController
public class UserController {

    @Autowired
    private UserService userService;

    @GetMapping("/")
    public String index(){
        return "Hello World";
    }

    @GetMapping("/query")
    public User queryUser(){
        return userService.findUserByName("douglee");

    }

}
```

### 运行&测试

DemoApplication.java

```java
@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }

}
```

运行DemoApplication, 并访问http://localhost:8080/query

最后的项目结构如图

![img](..\images\spring_boot2\2.png)

## 全注解的方式使用MyBatis

注解的方式不需要Mapper.xml, 而是直接在dao层以注解的方式写sql语句,

UserDao.java

```java
@Mapper
public interface UserDao {
    /**
     * 通过用户名查找用户
     * @param name
     * @return
     */

    @Select("SELECT * FROM user WHERE name=#{name}")
    User findUserByName(@Param("name") String name);
}
```

此外需要把application.yml中的**mybatis.mapper-locations=classpath:mapper/*.xml** 去掉

然后同样运行DemoApplication.java,并访问http://localhost:8080/query  测试成功.

