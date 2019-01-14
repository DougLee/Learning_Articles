## Spring Data Jpa 使用

## Spring Data Jpa简介

### 关于Spring Data

Spring Data是Spring社区的一个顶级工程, 主要用于简化数据库访问.

## maven 依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<!-- spring-boot-starter-data-jpa -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<!-- mysql-connector-java -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
```



## 系统的相关配置application.yml

```yml
spring:
  datasource:
    url: jdbc:mysql://localhost/springboot-demo
    username: cnki
    password: cnki
    driver-class-name: com.mysql.cj.jdbc.Driver
```

## entity 层

```java
// com/example/domain/User.java
@Entity
@Table(name = "user")
public class User implements Serializable {
    private static final long serialVersionUID = 318278932974580793L;

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;

    @Column(name = "name")
    private String name;

    @Column(name = "age")
    private Integer age;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }
}
```



## dao层

```java
// com.example.repository.UserRepository
public interface UserRepository extends JpaRepository<User, Long> {
}
```

## Controller层

```java
// com.example.controller.UserController
@RestController
public class UserController {

    @Autowired
    private UserRepository userRepository;

    @GetMapping("/")
    public String index(){
        return "Hello Spring Data Jpa";
    }

    @GetMapping("/selectAll")
    private List<User> selectAll(){

        return userRepository.findAll();
    }
}
```

## 启动类

```java
// com.example.SpringBootJpaApplication
@SpringBootApplication
public class SpringBootJpaApplication {
    public static void main(String[] args) {
        SpringApplication.run(SpringBootJpaApplication.class, args);
    }
}
```

## ## 验证

启动SpringBootJpaApplication后, 在浏览器访问http://localhost:8080/selectAll.