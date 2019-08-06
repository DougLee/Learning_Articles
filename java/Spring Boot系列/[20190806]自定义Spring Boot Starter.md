# 自定义Spring Boot Starter



## Spring Boot Starter是什么

Spring Boot集成一些第三方库, 基于"约定优于配置"的原则, 简化了各种配置并通过Starter引入, 使用户可以快速开发并部署Spring应用.

这些Spring本身和第三方库提供的功能都是由**AutoConfigure**相关的功能实现的, 可以说Spring Boot = SpringFramework + AutoConfigure. Spring Boot默认已经提供了大部分常用库的支持, 但是我们仍然可以自定义开发starter, 让用户可以更快更简单的引入相关的功能.

## Spring Boot Starter的原理



## 自定义Spring Boot Starter

### 1.  新建一个Maven项目 (maven-archetype-quickstart)

项目命名为demo-spring-boot-starter. 

#### spring boot starter命名规则

+ 官方starter: spring-boot-starter-{name}
+ 第三方starter: {name}-spring-boot-starter

命名规则只是一个约定俗成的规定, 建议遵循这个命名规则.

#### 引入必要的依赖

Starter需要使用Spring boot的自动配置功能, 所以要引入相关的依赖.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-autoconfigure</artifactId>
    <version>2.1.6.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <version>2.1.6.RELEASE</version>
</dependency>
```

### 2.  创建Properties文件

Properties文件用来接收application.properties文件中的参数配置, 可以修改配置文件中的相关属性值来覆盖改属性的默认配置

代码如下:

```java
@ConfigurationProperties(prefix = "spring.demo")
public class DemoProperties {

    private String name = "douglee";
    private String msg = "hello";

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getMsg() {
        return msg;
    }

    public void setMsg(String msg) {
        this.msg = msg;
    }
}
```



### 3. 创建服务类

```java
public class DemoService {
    private DemoProperties properties;

    public DemoService(DemoProperties properties) {
        this.properties = properties;
    }

    public void demo() {
        System.out.println(properties.getMsg() + ": " +properties.getName());
    }
}
```



### 4. 创建自动配置类

```java
@Configuration
@EnableConfigurationProperties(DemoProperties.class)
@ConditionalOnClass(DemoService.class)
@ConditionalOnProperty(prefix = "spring.demo", value = "enabled", matchIfMissing = true)
public class DemoServiceAutoConfiguration {
    @Autowired
    private DemoProperties properties;
    
    @Bean
    @ConditionalOnMissingBean(DemoService.class) // 容器没有指定Bean的时候, 自动配置DemoService
    public DemoService demoService() {
        DemoService demoService = new DemoService(properties);
        return demoService;
    }
}
```



### 5. 创建spring.factories文件

创建一个src/main/resources/META-INF/spring.factories文件. 内容如下, 在该文件中配置自己的自动配置类.

src/main/resources需要标注为资源文件夹, 否则会找不到starter的自动装配类.

```
org.springframework.boot.autoconfigure.EnableAutoConfiguration=com.douglee.DemoServiceAutoConfiguration
```



## 测试 Demo-spring-boot-starter

### 打包demo-spring-boot-starter: mvn clean install

### 创建一个spring boot 项目并引入demo-spring-boot-starter依赖

```xml
<dependency>
    <groupId>com.douglee</groupId>
    <artifactId>demo-spring-boot-starter</artifactId>
    <version>1.0-SNAPSHOT</version>
</dependency>
```

### 配置application.properties

```
spring.demo.msg=Hello123
spring.demo.name=James
```

### 测试

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class TestDemoStarter {

    @Autowired
    private DemoService demoService;

    @Test
    public void testDemo() {
        demoService.demo();
    }
}
```

运行测试程序会输出: Hello123: James.

