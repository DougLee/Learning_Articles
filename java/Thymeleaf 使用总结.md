## Thymeleaf 介绍

[Thymeleaf](http://www.thymeleaf.org/) 是一个模板引擎, 是Spring Boot的推荐的Java模板引擎, Thymeleaf跟Velocity, FreeMarker类似, 可以完全替代JSP. 

## Thymeleaf 基本语法

### 简单表达式

* ${...} 变量表达式
* *{...} 选择变量表达式
* \#{...} 消息表达式
* @{...} 连接URL表达式

#### 变量表达式

```html
<p>
  My name is <span th:text="${name}">name</span>
</p>
```

> 如果name的值为douglee, 则渲染后的值将为My name is douglee. span的值name将会被覆盖

#### 选择变量表达式

选择变量表达式也叫星号表达式,  可以简单理解为内层是对外层对象的引用.

``` html
<div th:object="${session.user}">
    <p>Name: <span th:text="*{firstName}">Sebastian</span>.</p>
    <p>Surname: <span th:text="*{lastName}">Pepper</span>.</p>
    <p>Nationality: <span th:text="*{nationality}">Saturn</span>.</p>
</div>
```

等同于下面的方式:

```html
<div>
  <p>Name: <span th:text="${session.user.firstName}">Sebastian</span>.</p>
  <p>Surname: <span th:text="${session.user.lastName}">Pepper</span>.</p>
  <p>Nationality: <span th:text="${session.user.nationality}">Saturn</span>.</p>
</div>
```

两者也可以混用.如果没有th:object结合使用, *{}和${}效果一样.

#### 消息表达式

#### URL表达式

### 常用th标签

| 关键字  | 功能介绍 | 例子   |
| :--- | :--- | :--- |
|      |      |      |



## 使用Thymeleaf布局



## 参考连接

1. [thymeleaf使用详解](http://www.ityouknow.com/springboot/2016/05/01/springboot(%E5%9B%9B)-thymeleaf%E4%BD%BF%E7%94%A8%E8%AF%A6%E8%A7%A3.html)
2. ​









