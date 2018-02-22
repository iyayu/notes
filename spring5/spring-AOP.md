# Spring Aop
AOP 面向切面变成, 是基于动态代理实现的.

AOP 通知类型:
 - **前置通知**: 在目标方法前执行.
 - **后置通知**: 在目标方法后执行.
 - **环绕通知**: 在目标方法前后都执行.
 - **异常通知**: 在抛出异常后执行.
 - **最终通知**: 当某连接点退出的时候执行的通知（不论是正常返回还是异常退出）.
 
> 在Spring配置中, 所有方面 切入点和通知元素都必须放置在 ```<aop:config>``` 元素中.
 
## 声明一个方面(切面 Aspect)
```
<aop:config>
    <aop:aspect id="myAspect" ref="aBean">
        ...
    </aop:aspect>
</aop:config>

<bean id="aBean" class="...">
    ...
</bean>
```

方面使用 ```<AOP:aspect>``` 元素声明, 支持bean使用 ```ref``` 属性引用.

```
<bean id="aBean" class="...">
    ...
</bean>
```
最终我们会将这个类中的某个方法进行切入, 切入的方法就叫**通知**.

## 声明一个切入点(Pointcut)
```
<aop:config>

    <aop:pointcut id="businessService"
        expression="execution(* com.xyz.myapp.service.*.*(..))"/>

</aop:config>
```
```* com.xyz.myapp.service.*.*(..)``` 这个表达式指定我们要将通知切入哪个类. 我们将切入 ```service``` 包下的所有类所有方法.

当然你也可以写的更详细一些, 例如```com.xyz.myapp.SystemArchitecture.businessService()```

## 声明通知
@AspectJ风格支持五种通知类型.
**前置通知 <aop:before>**
```
<aop:aspect id="beforeExample" ref="aBean">

    <aop:before
        pointcut-ref="dataAccessOperation"
        method="doAccessCheck"/>

</aop:aspect>
```
```pointcut-ref``` 属性表示我们要将通知作用在哪个切入点上.

```method``` 属性表示我们要切入哪个方法.

我们也可以使用 ```pointcut``` 属性代替 ```pointcut-ref``` 属性.
```
    <aop:before
        pointcut="execution(* com.xyz.myapp.dao.*.*(..))"
        method="doAccessCheck"/>
```

**后置通知**
 ```
     <aop:after-returning
        pointcut-ref="dataAccessOperation"
        method="doAccessCheck"/>
 ```
 
**环绕通知**
```
    <aop:around
        pointcut-ref="businessService"
        method="doBasicProfiling"/>
```

**异常通知**
 ```
     <aop:after-throwing
        pointcut-ref="dataAccessOperation"
        method="doRecoveryActions"/>
 ```

**最终通知**
```
    <aop:after
        pointcut-ref="dataAccessOperation"
        method="doReleaseLock"/>
```

