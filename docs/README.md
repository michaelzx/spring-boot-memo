# 快速构建工程

使用官方提供的`Spring Initializr`，来快速生成Spring boot应用骨架

1. 打开网址：https://start.spring.io/
2. 搜索并添加依赖
3. 生成并下载

# Web

核心依赖包
```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

## 请求处理

> 主要列举下几个核心注解

### @Contrller

* 位置：类
* 作用：表示这个类是有一个控制器
* 说明：一般是需要搭配一个`@RequestMapping`注解，配置这个控制器的路径
* 注意：在没有`@ResponseBody`搭配的情况下，需要增加模板引擎依赖否则会报错

### @ResponseBody

* 位置：类或方法
* 作用：告诉控制器，这个Action甚至整个Controller，返回的对象都将会序列化成JSON，并回传给HttpResponse

### @RestController

* 位置：类
* 作用：快捷注解，相当于给类添加`@Contrller`+`@ResponseBody`，2个注解

### @RequestMapping

* 位置：类或方法
* 作用：几个参数及作用

* value：指定请求的实际地址，指定的地址可以是URI Template 模式（后面将会说明）；
* method：指定请求的method类型， GET、POST、PUT、DELETE等；
* consumes：指定处理请求的提交内容类型（Content-Type），例如application/json, text/html;
* produces：指定返回的内容类型，仅当request请求头中的(Accept)类型中包含该指定类型才返回；
* params：指定request中必须包含某些参数值是，才让该方法处理。
* headers：指定request中必须包含某些指定的header值，才能让该方法处理请求。

### @GetMapping
* 位置：类或方法
* 作用：快捷注解，作用同`@RequestMapping`,只是限定了method=GET


### @PostMapping
* 位置：类或方法
* 作用：快捷注解，作用同`@RequestMapping`,只是限定了method=POST

## 获取请求数据

### 获取url参数

例如：获取以下请求中的userName参数

```
/user/update/123?userName=zhangshan
```

代码示例：
```java
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping(value = "/user")
public class TestController {

    @PostMapping(value = "/update/{userId}")
    public String update(@RequestParam String userName){
        return userName;
    }
}
```
> 和请求方式无关

### 获取Form参数

> 和上面的一样，因为是form参数，所以请求方式，必须是POST

### 获取url中的path变量

1. 需要预先在`@RequestMapping`中定义
2. 给参数加上@PathVariable

例如：获取以下请求url中的123

```
/user/update/123?userName=zhangshan
```

代码示例：

```java
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping(value = "/user")
public class TestController {
    @PostMapping(value = "/update/{userId}")
    public String update(
            @RequestParam String userName,
            @PathVariable String userId
    ){
        return userId+"-"+userName;
    }
}
```
### 将请求参数绑定到java对象上

TestBean.java
```java
import lombok.Data;

@Data
public class TestBean {
    private Integer userId;
    private String userName;
}
```
TestController.java
```java
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping(value = "/user")
public class TestController {

    @PostMapping(value = "/update/{userId}")
    public String update(
        TestBean testBean
    ){
        return testBean.getUserId()+"-"+testBean.getUserName();
    }
}
```
经过测试，直接将请求参数绑定到java对象。优先级如下：

1. url的query参数
2. form的参数
3. url的path参数

如果多处存在相同名称参数，优先级高的会覆盖优先级低的

### 获取json请求，并绑定到java对象
```java
package com.example.demo;

import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping(value = "/user")
public class TestController {

    @PostMapping(value = "/update/{userId}")
    public String update(
        @RequestBody TestBean testBean
    ){
        return testBean.getUserId()+"-"+testBean.getUserName();
    }
}
```
**@RequestBody**

- 该注解，可以将客户端，发起post请求时，将request body中的json，直接映射成java对象
- json可以拿过来提交嵌套结构的对象，降低参数接收的复杂度


## 数据校验


### @Valid 和 @Validated 
`@Valid`
  - 所在包：javax.validation.Valid
  - 属于JSR-303规范

`@Validated`
- 所在包：org.springframework.validation.annotation.Validated
- @Validated是@Valid的一次封装，不是规范。
- `@Validated`相对于`@Valid`,增加了分组校验
- **在spirng开发中，我想应该更加推荐使用**

### 基本验证规则

<table>
  <tr>
    <th>注解</th>
    <th>可校验类型</th>
    <th>说明</th></tr>
  <tr>
    <td>@AssertFalse</td>
    <td>Boolean,boolean</td>
    <td>验证注解的元素值是false</td></tr>
  <tr>
    <td>@AssertTrue</td>
    <td>Boolean,boolean</td>
    <td>验证注解的元素值是true</td></tr>
  <tr>
    <td>@NotNull</td>
    <td>任意类型</td>
    <td>验证注解的元素值不是null</td></tr>
  <tr>
    <td>@Null</td>
    <td>任意类型</td>
    <td>验证注解的元素值是null</td></tr>
  <tr>
    <td>@Min(value=值)</td>
    <td>BigDecimal，BigInteger, byte,short, int, long，等任何Number或CharSequence（存储的是数字）子类型</td>
    <td>验证注解的元素值大于等于@Min指定的value值</td></tr>
  <tr>
    <td>@Max（value=值）</td>
    <td>和@Min要求一样</td>
    <td>验证注解的元素值小于等于@Max指定的value值</td></tr>
  <tr>
    <td>@DecimalMin(value=值)</td>
    <td>和@Min要求一样</td>
    <td>验证注解的元素值大于等于@ DecimalMin指定的value值</td></tr>
  <tr>
    <td>@DecimalMax(value=值)</td>
    <td>和@Min要求一样</td>
    <td>验证注解的元素值小于等于@ DecimalMax指定的value值</td></tr>
  <tr>
    <td>@Digits(integer=整数位数, fraction=小数位数)</td>
    <td>和@Min要求一样</td>
    <td>验证注解的元素值的整数位数和小数位数上限</td></tr>
  <tr>
    <td>@Size(min=下限, max=上限)</td>
    <td>字符串、Collection、Map、数组等</td>
    <td>验证注解的元素值的在min和max（包含）指定区间之内，如字符长度、集合大小</td></tr>
  <tr>
    <td>@Past</td>
    <td>java.util.Date,java.util.Calendar;Joda Time类库的日期类型</td>
    <td>验证注解的元素值（日期类型）比当前时间早</td></tr>
  <tr>
    <td>@Future</td>
    <td>与@Past要求一样</td>
    <td>验证注解的元素值（日期类型）比当前时间晚</td></tr>
  <tr>
    <td>@NotBlank</td>
    <td>CharSequence子类型</td>
    <td>验证注解的元素值不为空（不为null、去除首位空格后长度为0），不同于@NotEmpty，@NotBlank只应用于字符串且在比较时会去除字符串的首位空格</td></tr>
  <tr>
    <td>@Length(min=下限, max=上限)</td>
    <td>CharSequence子类型</td>
    <td>验证注解的元素值长度在min和max区间内</td></tr>
  <tr>
    <td>@NotEmpty</td>
    <td>CharSequence子类型、Collection、Map、数组</td>
    <td>验证注解的元素值不为null且不为空（字符串长度不为0、集合大小不为0）</td></tr>
  <tr>
    <td>@Range(min=最小值, max=最大值)</td>
    <td>BigDecimal,BigInteger,CharSequence, byte, short, int, long等原子类型和包装类型</td>
    <td>验证注解的元素值在最小值和最大值之间</td></tr>
  <tr>
    <td>@Email(regexp=正则表达式,flag=标志的模式)</td>
    <td>CharSequence子类型（如String）</td>
    <td>验证注解的元素值是Email，也可以通过regexp和flag指定自定义的email格式</td></tr>
  <tr>
    <td>@Pattern(regexp=正则表达式,flag=标志的模式)</td>
    <td>String，任何CharSequence的子类型</td>
    <td>验证注解的元素值与指定的正则表达式匹配</td></tr>
  <tr>
    <td>@Valid</td>
    <td>任何非原子类型</td>
    <td>指定递归验证关联的对象；如用户对象中有个地址对象属性，如果想在验证用户对象时一起验证地址对象的话，在地址对象上加@Valid注解即可级联验证</td></tr>
</table>