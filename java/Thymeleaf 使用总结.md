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

```html
<a href="main.html" th:href="@{/main}"/>
```

可以支持设置参数和相对路径

**@{/order/details(id=${orderId})} **

**@{../documents/report}**

### 常用th标签

| 关键字  | 功能介绍 | 例子   |
| :--- | :--- | :--- |
|      |      |      |



## 使用Thymeleaf模板布局

### 定义和引用模板

在Thymeleaf中可以使用**th:fragment**属性来定义一个模板

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.w3.org/1999/xhtml">
<div th:fragment="footer" th:class="footer">
    2018 &copy;
</div>
</html>
```

上面定义了一个叫做**footer**的模板, 可以通过**th:include**或者**th:replace**属性来使用它.

```html
<div th:include="fragments/footer :: footer"></div>
<div th:replace="fragments/footer :: footer"></div>
```

> th:include和th:replace的区别: include只是加载, replace是替换.

比如上面两个最终渲染的效果, th:replace会包含class="footer"这个属性, 而th:include则没有包含这个属性.

## Others



## 参考连接

1. [thymeleaf使用详解](http://www.ityouknow.com/springboot/2016/05/01/springboot(%E5%9B%9B)-thymeleaf%E4%BD%BF%E7%94%A8%E8%AF%A6%E8%A7%A3.html)
2. ​









