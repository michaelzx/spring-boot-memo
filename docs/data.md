# Data层

> 一开始的时候，是使用了一段时间JPA，感觉到后面业务复杂的时候，不够灵活。
>
> 后来尝试了Mybits，感觉很棒，非常灵活和使用，而且还有现成的分页插件可以使用。
>
> 虽然需要自己手写一些sql，但是自己写的sql，用着安心。有问题也知道去哪里找。

## mybatis的集成及使用

### 添加依赖

pom.xml
```
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.21</version>
</dependency>
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>1.3.1</version>
</dependency>
```

### 配置

application.yml

```
spring:
  datasource:
    url: jdbc:mysql://服务器:端口/数据库名?useSSL=false&autoReconnect=true&allowMultiQueries=true
    username: 用户名
    password: 密码
```

### 数据模型分类
分层领域模型规约:
- DO(Data Object):与数据库表结构一一对应,通过 DAO 层向上传输数据源对象。
- DTO(Data Transfer Object):数据传输对象,Service 或 Manager 向外传输的对象。
- BO(Business Object):业务对象。由 Service 层输出的封装业务逻辑的对象。
- AO(ApplicationObject):应用对象。在Web层与Service层之间抽象的复用对象模型, 极为贴近展示层,复用度不高。
- VO(View Object):显示层对象,通常是 Web 向模板渲染引擎层传输的对象。
- Query:数据查询对象,各层接收上层的查询请求。注意超过 2 个参数的查询封装,禁止使用 Map 类来传输。

> 摘自:[《阿里巴巴Java开发手册》](https://github.com/alibaba/p3c)

## 分页插件的使用
?> 待完善
## 嵌套对象的查询
?> 待完善


## 参考文章

> http://www.spring4all.com/article/145
> 
> http://blog.didispace.com/springbootmybatis/