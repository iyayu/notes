# 带注解的控制器

如果要使用SpringMvc注解, 要在XML文件中使用 ```<context:component-scan base-package="org.example.web"/>``` 配置来开启注解.

```
//使用Controller注解标识一个控制器
@Controller
public class TestController{

    //@RequestMapping实现对testMethod方法和url进行映射
    //注意:@RequestMapping注解中的value值可以随便指定的但是要有意义,只不过习惯写成方法名而已.
    @RequestMapping(value = "testMethod")
    public ModelAndView testMethod(){
        //例如我们的这个测试的Handler是用来做查询用户测试的
        //这里会调用service的查询用户的功能.

        //这个方法需要返回一个ModelAndView对象所以我们创建一个.
        ModelAndView modelAndView = new ModelAndView();

        //这个方法相当于request的setAttribute()方法.
        modelAndView.addObject("key", "value");

        //指定视图
        //例如我们在/WEB-INF/jsp/test.jsp页面,所以我们的视图名称就要写成如下.
        modelAndView.setViewName("/WEB-INF/jsp/test.jsp");
        return modelAndView;
    }
}
```

## 请求映射
```@RequestMapping``` 注释用于将请求映射到控制器方法. 它具有各种属性, 可以通过URL、HTTP方法、请求参数、标头和媒体类型进行匹配.

它可以在类级别用于表示共享映射, 也可以在方法级别用于缩小到特定端点映射. 还有HTTP方法特定的 ```@RequestMapping``` 的快捷方式变体:
 - @GetMapping
 - @PostMapping
 - @PutMapping
 - @DeleteMapping
 - @PatchMapping

```
@RestController
@RequestMapping("/persons")
class PersonController {

    @GetMapping("/{id}")
    public Person getPerson(@PathVariable Long id) {
        // ...
    }

    @PostMapping
    @ResponseStatus(HttpStatus.CREATED)
    public void add(@RequestBody Person person) {
        // ...
    }
}
```

**URL 模式**
您可以使用GLOB模式和通配符映射请求：
 - ```？``` 匹配一个字符
 - ```*``` 匹配路径段中的零个或多个字符。
 - ```**``` 匹配零或多个路径段
 
还可以声明URI变量并使用 ```@PathVariable```  访问它们的值:
```
@GetMapping("/owners/{ownerId}/pets/{petId}")
public Pet findPet(@PathVariable Long ownerId, @PathVariable Long petId) {

}
```

URI变量可以在类和方法级别声明:
```
@Controller
@RequestMapping("/owners/{ownerId}")
public class OwnerController {

    @GetMapping("/pets/{petId}")
    public Pet findPet(@PathVariable Long ownerId, @PathVariable Long petId) {

    }
}
```

URI变量会自动转换为适当的类型, 或者引发 ```TypeMismediameException``` . 默认情况下支持简单类型 ```int、long、date```.

URI变量可以显式命名 例如 ```@PathVariable(“customId”)```.

**媒体类型**
可以根据请求的内容类型缩小请求映射范围.
```
@PostMapping(path = "/pets", consumes = "application/json")
public void addPet(@RequestBody Pet pet) {

}
```

你可以使用这种写法 ```!text/plain``` 除 ```text/plain``` 外的其他类型.

如果方法和类都是用了这种方式, 则方法会覆盖类的设置.

您可以根据Accept请求头和控制器方法生成的内容类型列表缩小请求映射范围:
```
@GetMapping(path = "/pets/{petId}", produces = "application/json;charset=UTF-8")
@ResponseBody
public Pet getPet(@PathVariable String petId) {

}
```

**参数、标头**
可以根据请求参数条件缩小请求映射. 您可以测试请求参数(“myParam”)是否存在, 是否缺失(“！myParam”), 或者测试是否存在特定值(“myParam=myValue”):
```
@GetMapping(path = "/pets/{petId}", params = "myParam=myValue")
public void findPet(@PathVariable String petId) {

}
```

```
@GetMapping(path = "/pets", headers = "myHeader=myValue")
public void findPet(@PathVariable String petId) {

}
```

## Handler Methods




















