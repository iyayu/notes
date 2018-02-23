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
























