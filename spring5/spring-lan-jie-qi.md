# Spring 拦截器

定义拦截器需要实现 ```HandlerInterceptor```

```
public class TestIntercpter implements HandlerInterceptor {

    //在执行Handler方法之前运行
    //由于身份认证,身份授权.
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        //false: 拦截不继续往下执行
        //true: 继续往下执行
        return false;
    }

    //在执行Handler方法之后,再返回ModelAndView之前执行.
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        //我们可以操作ModelAndView对象,将一些公用的模型数据添加进去.比如菜单导航.
    }

    //Handler执行完成之后执行.
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        //统一的异常处理,统一的日志处理
    }
}
```

## 配置拦截器

> 注意:spring mvc的拦截器是针对HandlerMapping进行拦截设置.
> 如果在某个HandlerMapping中配置拦截器,经过该HandlerMapping映射成功的Handler才会被拦截器拦截.

```
    <mvc:interceptors>
        <mvc:interceptor>
            <!-- /**拦截所有URL 包括子URL -->
            <mvc:mapping path="/**"/>
            <bean class="cc.test.TestIntercpter"/>
        </mvc:interceptor>
        <!-- 可以配置多个拦截器 -->
    </mvc:interceptors>
```
