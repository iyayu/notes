# Spring MVC 基础
Spring Web MVC是建立在servlet API上的Web框架. 而且是围绕前端控制器模式设计的.

正式名称是“Spring Web MVC”来自其源模块 ```sprting-webmvc``` 名字通常被称为“Spring MVC”.

## 前端控制器模式 (Front Controller Pattern)
前端控制器设计模式就是由单一的一个处理程序来处理所有请求.

此处理程序可以执行请求的身份验证/授权/记录或跟踪, 然后将请求传递到相应的处理程序. 以下是这种类型的设计模式的实体.
 - **前端控制器(Front Controller)**: 处理所有请求的单个处理程序.
 - **处理器(Handler)**: 当前端控制器接收到请求时, 会找到这个请求的处理器并由这个处理器处理请求.
 - **视图(View)**: 视图是为请求而创建的对象.

## DispatcherServlet
在上面我们说过 SpringMVC 是围绕前端控制器模式设计的. 所有请求都是通过前端控制器来进行处理. 所以在 SpringMVC 中前端控制器就是 ```DispatcherServlet```. 

我们可以通过 ```web.xml``` 配置文件来配置
```
<web-app>

    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>/WEB-INF/app-context.xml</param-value>
    </context-param>

    <servlet>
        <servlet-name>app</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value></param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>app</servlet-name>
        <url-pattern>/app/*</url-pattern>
    </servlet-mapping>

</web-app>
```

由于Web应用程序入口是被Web服务器控制的, 所以无法在 ```main()``` 方法中通过创建 ```ClassPathXmlApplicationContext``` 对象来启动Spring容器.

Spring提供了一个监听类 ```org.srpingframework.web.context.ContextLoaderListener``` 来解决这个问题. 该监听器实现了```ServletContextListener``` 接口, 可以在Web容器启动的时候初始化Spring容器.

其中, ```contextConfigLocation``` 参数用来指定Spring配置文件的路径. 如果没有指定 ```contextConfigLocation``` 参数, ```ContextLoaderListener``` 默认会查找 ```/WEB-INF/applicationContext.xml``` .

换句话说,如果我们将Spring 的配置文件命名为 ```applicationContext.xml``` 并放在 ```WEB-INF``` 目录下,即使不指定 ```contextConfigLocation``` 参数,也能实现配置文件的加载.

```DispatcherServlet``` 对象中的 ```contextConfigLocation``` 属性是用来配置 SpringMVC 的. 如果没有设置值, 默认加载的是 ```/WEB-INF/servlet名称-servlet.xml``` 例如 ```/WEB-INF/servletapp-servlet.xml```

**关于url-pattern的配置**

url-pattern配置有三种:
 - ```*.do``` 访问以.do结尾的由 ```DispatcherServlet``` 进行解析.
 - ```/(斜杠)``` 所有访问的地址都由DispatcherServlet进行解析,对于静态的文件解析需要配置,不让DispatcherServlet进行解析. 注意:使用此种方式可以实现 RESTful风格的url.
 - ```/*``` 使用这种配置,最终要转发到一个jsp页面时,仍然会由DispatcherServlet进行解析.
 
## Spring MVC 执行流程
![Spring MVC 执行流程](https://upload-images.jianshu.io/upload_images/3938475-444f2b2c1af42583.png)

1. 用户发起请求到前端控制器(DispatcherServler)
2. 前端控制器调用 ```HandlerMapping``` (处理器映射器), 找到URL所对应的Handler.并返回一个 ```HandlerExecuteChain``` 对象.其中包含有拦截器链与URL对应的Handler.
也就是说 ```HandlerMapping``` 主要是帮我们查找要Handler,并返回一个 ```HandlerExecutChain``` 对象.
3. 调用 ```HandlerAdapter``` (处理器适配器)来执行Handler并返回 ```ModelAndView``` 对象.
4. 调用 ```ViewResolver``` (视图解析器), 将逻辑视图解析成物理视图并返回View对象. 例如我们ModelAndView中存放的视图名为"user"(逻辑视图),通过ViewResolver(视图解析器),解析为"/WEB-INF/user.jsp"(物理视图).
5. 前端控制器进行视图渲染,将模型数据填充到Request域中.
6. 前端控制器向用户响应结果.

## Spring MVC 与 Struts2对比
**入口**
Spring MVC入口是servlet,而Struts2是filter.
因为我们在配置web.xml的时候使用了不同的标签进行的配置.

**对request请求参数的封装**
SpringMVC的方法之间基本上独立的,独享request response数据,请求数据通过方法参数进行传递.
Struts2把request请求参数封装到Action的属性上.虽然方法之间也是独立的,但Action变量是共享的,也就是说我们每次请求就要创建一个Action.

**对于ajax**
SpringMVC集成了Ajax只需一个注解@ResponseBody就可以.
Struts2拦截器集成了Ajax,但是在Action中处理时需要先继承json-default

