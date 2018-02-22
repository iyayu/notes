# Bean范围&继承

## Bean范围
你不仅可以配置bean之间的依赖关系, 还可以创建bean的范围.
 - **singleton**: (默认)将单个bean定义为每个SpringIoC容器的单个对象实例.
 - **prototype**: 将单个bean定义的作用范围扩展到任意数量的对象实例. 也就是说每次获取都会创建一个新的.
 - **request**: 将单个bean定义的作用范围扩展到单个HTTP请求的生命周期. 也就是说每个HTTP请求都有自己的bean实例.
 - **session**: 将单个Bean定义扩展到HTTP会话的生命周期. 仅在基于Web的Spring应用程序上下文的上下文中有效.
 - **application**: 将单个bean定义作用于ServletContext的生命周期. 整个应用程序.
 - **websocket**: 将单个bean定义作用于WebSocket的生命周期.

```
<bean id="accountService" class="com.foo.DefaultAccountService" scope="prototype"/>
```

## Bean继承
bean定义可以包含许多配置信息, 包括构造函数参数、属性值和容器特定信息, 如初始化方法、静态工厂方法名称等. 子bean定义从父定义继承配置数据. 子定义可以根据需要覆盖某些值, 或者添加其他值. 使用父bean和子bean定义可以节省大量的输入.
```
<bean id="inheritedTestBean" abstract="true"
        class="org.springframework.beans.TestBean">
    <property name="name" value="parent"/>
    <property name="age" value="1"/>
</bean>

<bean id="inheritsWithDifferentClass"
        class="org.springframework.beans.DerivedTestBean"
        parent="inheritedTestBean" init-method="initialize">
    <property name="name" value="override"/>
</bean>
```

## 生命周期回调
**初始化回调**
```
<bean id="exampleInitBean" class="examples.ExampleBean" init-method="init"/>
```

**销毁回调**
```
<bean id="exampleInitBean" class="examples.ExampleBean" destroy-method="cleanup"/>
```


























