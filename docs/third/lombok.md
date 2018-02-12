# lombok

> Lombok是一个可以通过简单的注解形式来帮助我们简化消除一些必须有但显得很臃肿的Java代码的工具
>
> 通过使用对应的注解，可以在编译源码的时候生成对应的方法
>
> 官方地址：https://projectlombok.org/
>
> github地址：https://github.com/rzwitserloot/lombok

## 添加依赖

```
<dependency>
	<groupId>org.projectlombok</groupId>
	<artifactId>lombok</artifactId>
	<version>1.16.18</version>
</dependency>
```

## 安装IntelliJ IDEA插件

1. cmd+, 
2. Plugins
3. Browse repositoris
4. 搜索:Lombok Plugin

## 使用
自动生成Geter、Seter，可以使用`@Data`注解

```java
import lombok.Data;

@Data
public class City {
    private Long id;
    private Long provinceId;
    private String cityName;
    private String description;
}
```

之后，直接可以在写代码直接使用：
```java
City city = new City();
city.getId();
city.setId(??);
city.getProvinceId();
city.setProvinceId(??);
city.getCityName();
city.setCityName(??);
city.getDescription();
city.setDescription(??);
```