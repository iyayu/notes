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
在上面我们说过 SpringMVC 是围绕前端控制器模式设计的. 所有请求都是通过前端控制器来进行处理. 所以在 SpringMVC 中所有前端控制器就是 ```DispatcherServlet```. 
