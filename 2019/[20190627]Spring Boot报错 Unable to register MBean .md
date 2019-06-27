# Spring Boot中报错 Unable to register MBean..

## 背景

同一个Tomcat中部署了两个Spring Boot打的war包, 两个项目无法同时启动, 任意起其中一个都没有问题.

## 原因

每一个JMX MBean都必有有一个对象名称. 在当前的背景下,是因为两个war包项目同时引用了一个JMX MBean 的默认值, 导致他们名称冲突 所以无法注册.

## 解决办法

在项目中添加配置

```yml
spring.jmx.default-domain: some-name # 取一个自己项目的不重复的名字
或者
spring.jmx.enabled: false
```

