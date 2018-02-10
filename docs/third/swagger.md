# Swagger 集成及使用
> 对于前后端分离的开发方式来说，Api文档至关重要   
> 借助Swagger，可以在写代码的时候，就把文档完善好。方便协作开发



## 添加依赖

使用 [SpringFox](http://springfox.github.io/springfox/) 来集成 Swagger

pom.xml
```xml
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.7.0</version>
</dependency>
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.7.0</version>
</dependency>
```

!> 2.8.0中将 Swagger UI升级到了3.1.5。模拟form提交的时候感觉还是有点问题，试了下还是用2.7.0的UI好了


## 最简单配置

```java
package com.example.demo;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

@Configuration
@EnableSwagger2
public class SwaggerConfig {

    @Bean
    public Docket apiDocDemo() {
        Docket docket = new Docket(DocumentationType.SWAGGER_2);
        docket.select()
        		.apis(RequestHandlerSelectors.any())
                .paths(PathSelectors.any())
                .build();
        return docket;
    }
}

```

## 高级配置

```java
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.service.Contact;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

@Configuration
@EnableSwagger2
public class Swagger2Config {

    @Bean
    public Docket userApi() {
        return docketInit("用户","user","com.example.demo.web.controller.user");
    }
    @Bean
    public Docket staffApi() {
        return docketInit("员工","staff","com.example.demo.web.controller.staff");
    }
    @Bean
    public Docket adminApi() {
        return docketInit("管理员","admin","com.example.demo.web.controller.admin");
    }
    @Bean
    public Docket guestApi() {
        return docketInit("访客","guest","com.example.demo.web.controller.guest");
    }

    private Docket docketInit(String title,String groupName,String apiPackage) {
        Contact contact = new Contact("Michael.Z","http://michaelzx.github.io","mail#zhangxiao.org");
        ApiInfo apiInfo = new ApiInfoBuilder()
                .title(title)
                .description("Api接口文档")
                .contact(contact)
                .version("0.0.1")
                .build();
        Docket docket = new Docket(DocumentationType.SWAGGER_2)
        		.pathMapping("/api");
        		.apiInfo(apiInfo)
                .groupName(groupName)
                .select()
                .apis(RequestHandlerSelectors.basePackage(apiPackage))
                .paths(PathSelectors.any())
                .build();
        return docket;
    }

}

```

## Swagger UI


项目启动完成后，可以通过 [localhost:8080/swagger-ui.html](localhost:8080/swagger-ui.html) 访问Swagger UI



## 配置form或query请求参数

```java
package com.example.demo;

import io.swagger.annotations.Api;
import io.swagger.annotations.ApiImplicitParam;
import io.swagger.annotations.ApiImplicitParams;
import io.swagger.annotations.ApiOperation;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;

@Api(
        value = "stu",
        description = "学生管理"
)
@Controller
@RequestMapping(value = "/stu")
public class TestController {

    @ApiOperation(value = "保存学生信息", response = Stu.class)
    @ApiImplicitParams({
            @ApiImplicitParam(name = "stuId", value = "学号", required = true,dataType = "int", paramType = "form"),
            @ApiImplicitParam(name = "stuName", value = "姓名", required = true,dataType = "string", paramType = "query"),
    })
    @PostMapping(value = "/save")
    @ResponseBody
    public Stu save(
        @RequestParam Integer stuId,
        @RequestParam String stuName
    ){
        Stu stu = new Stu();
        stu.setStuId(stuId);
        stu.setStuName(stuName);

        return stu;
    }
}
```


## 配置JSON请求对象

Book.java

```java
import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;
import lombok.Data;

@ApiModel(value = "课本")
@Data
public class Book {
    @ApiModelProperty(name = "课本ID", required = true)
    private Integer bookId;
    @ApiModelProperty(name = "课本名称", required = true)
    private String bookName;
}
```

Stu.java

```java
import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;
import lombok.Data;

import java.util.List;

@ApiModel(value = "学生", subTypes = Book.class)
@Data
public class Stu {

    @ApiModelProperty(name = "学号", required = true)
    private Integer stuId;

    @ApiModelProperty(name = "姓名", required = true)
    private String stuName;

    @ApiModelProperty(name = "课本列表", required = true)
    private List<Book> bookList;
}
```

TestController.java

```java
import io.swagger.annotations.Api;
import io.swagger.annotations.ApiOperation;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Api(
        value = "stu",
        description = "学生管理"
)
@Controller
@RequestMapping(value = "/stu")
public class TestController {

    @ApiOperation(value = "保存学生信息")
    @PostMapping(value = "/save")
    @ResponseBody
    public Stu save(
        @RequestBody  Stu stu
    ){
        return stu;
    }
}
```



