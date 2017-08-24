
## Shiro 简介

三大核心组件: Subject -> SecurityManager -> Realm

+ Subject: 当前用户操作
+ SecurityManager: 用于管理所有的Subject
+ Realm: 用于进行权限信息的验证, 需要自己实现Reamls中的Authentication和Authorization. 其中, Authentication是用来验证用户身份, Authorization是授权访问控制.

ShiroFilterFactory

SecurityManager

Realm

缓存管理 记住登录

## Authentication认证

+ Principals(身份)

+ Credentials(证明)


## Authorization(授权)

+ Permissions(权限)

+ Roles(角色)

+ Users(用户)
