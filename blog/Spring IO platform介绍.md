## 什么是Spring IO Platform

简单来说Spring IO Platform就是将各种spring API结合到一起, 提供可以一起正常使用的版本号, 可以理解为一个依赖维护平台.

## 为什么要使用Spring IO Platform

Spring IO Platform主要解决了版本依赖的问题, 之前升级项目的时候可能会需要一个个手动的升级各个模块的版本, 而且各个版本之间依赖是否合适并不知道. Spring IO Platform可以结合maven或者gradle管理各个模块的依赖. 在我们添加第三方依赖的时候, 不需要写版本号, Spring IO Platfrom可以自动帮我们挑选一个最优的版本.

## 在Maven中使用Spring IO Platform
在maven中使用Spring IO Platform有两种方式, 一种是import导入, 一种是继承parent.

### import导入的方式

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>io.spring.platform</groupId>
            <artifactId>platform-bom</artifactId>
            <version>Brussels-SR6</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```


### 继承parent的方式

```xml
<parent>
    <groupId>io.spring.platform</groupId>
    <artifactId>platform-bom</artifactId>
    <version>Brussels-SR6</version>
    <relativePath/>
</parent>

```

继承parent的方法，还会继承一些plugin management, 这些pulugin需要显式的添加, 例如spring boot的如下
```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

## Spring IO Platform中维护的依赖
[appendix-dependency-versions](https://docs.spring.io/platform/docs/current/reference/html/appendix-dependency-versions.html)
