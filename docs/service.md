# Service层

!> 一般情况下，业务逻辑都写在Service中，控制器中尽量不要出现

```java
import org.springframework.stereotype.Service;

@Service
public class MyService {
    public void serviceMethod(String text) {
        System.out.println(text);
    }
}

```
`@Service`，是一个特殊的`@Component`，表明这个类是一个Service，被它注解的类，将会在启动时被检测到

如要在控制器中使用，可以通过`@Autowired`来进行自动装配

```java
@RestController
@RequestMapping(value = "/stu")
public class TestController {
    @Autowired
    private MyService myService;

    @PostMapping(value = "/test")
    public void test(){
        myService.serviceMethod("test");
    }
}

```