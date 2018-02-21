# 实例化 Bean
bean定义就是创建一个或多个对象. 当被访问时, 容器会查看bean的配置, 并使用该bean配置的元数据来创建(或获取)实际对象.

```class``` 属性的两种使用方式:
 - 容器通过反射调用其构造函数直接创建bean, 相当于调用 ```new``` 关键字来创建对象. ```class``` 属性指定了需要创建bean的类.
 - 调用某个类静态的工厂方法来创建bean, class属性指定了实际包含静态工厂方法的那个类. 通过静态工厂方法调用返回的对象类型可能是同一个类或另一个类.
 
 ## 通过构造函数创建bean
 当您通过构造函数方法创建一个bean时, 这个类不需要实现任何特定的接口或以特定的方式编码.
 
 你可以根据你的需要使用默认构造函数或有参构造函数进行创建.
 
 **默认构造函数创建**
 ```
<bean id="exampleBean" class="examples.ExampleBean"/>

<bean name="anotherExample" class="examples.ExampleBeanTwo"/>
 ```
 
  **有参构造函数进行创建**
 ```
package x.y;

public class Foo {

    public Foo(Bar bar, Baz baz) {
        // ...
    }
}
 ```

```
<beans>
    <bean id="foo" class="x.y.Foo">
        <constructor-arg ref="bar"/>
        <constructor-arg ref="baz"/>
    </bean>

    <bean id="bar" class="x.y.Bar"/>

    <bean id="baz" class="x.y.Baz"/>
</beans>
```

## 通过静态工厂方法创建Bean
当你定义一个使用静态工厂方法创建的bean, 同时使用class属性指定包含静态工厂方法的类, 这个时候需要 ```factory-method``` 属性来指定工厂方法名.

下面是一个bean定义的例子, 声明这个bean要通过 ```factory-method``` 指定的方法创建. 在这个例子中, ```createInstance``` 必须是static方法.
```
<bean id="clientService"
    class="examples.ClientService"
    factory-method="createInstance"/>
```

```
public class ClientService {
    private static ClientService clientService = new ClientService();
    private ClientService() {}

    public static ClientService createInstance() {
        return clientService;
    }
}
```

> 注意这个bean定义并没有指定返回对象的类型, 只指定包含工厂方法的类.

## 通过实例工厂方法创建bean
使用一个实例工厂方法(非静态的)创建bean和使用静态工厂方法非常类似, 调用一个已存在的bean(这个bean应该是工厂类型)的工厂方法来创建新的bean.

使用这种机制, class属性必须为空, 而且 ```factory-bean``` 属性必须指定一个bean的名字, 这个bean一定要在当前的bean工厂或者父bean工厂中, 并包含工厂方法. 而工厂方法本身仍然要通过 ```factory-method``` 属性设置.
```
<bean id="serviceLocator" class="examples.DefaultServiceLocator">
</bean>

<bean id="clientService"
    factory-bean="serviceLocator"
    factory-method="createClientServiceInstance"/>
```

```
public class DefaultServiceLocator {

    private static ClientService clientService = new ClientServiceImpl();

    public ClientService createClientServiceInstance() {
        return clientService;
    }
}
```

一个工厂类也可以拥有多个工厂方法, 如下所示:
```
<bean id="serviceLocator" class="examples.DefaultServiceLocator">
</bean>

<bean id="clientService"
    factory-bean="serviceLocator"
    factory-method="createClientServiceInstance"/>

<bean id="accountService"
    factory-bean="serviceLocator"
    factory-method="createAccountServiceInstance"/>
```

```
public class DefaultServiceLocator {

    private static ClientService clientService = new ClientServiceImpl();

    private static AccountService accountService = new AccountServiceImpl();

    public ClientService createClientServiceInstance() {
        return clientService;
    }

    public AccountService createAccountServiceInstance() {
        return accountService;
    }
}
```

## 实例化静态内部类
```
public class Foo {
    public Foo() {
        System.out.println("Foo");
    }

    static class Bar {

        public Bar() {
            System.out.println("Bar");
        }

        public void show() {
            System.out.println("");
        }
    }

}
```

```
<bean id="bar" class="cc.iyayu.Foo$Bar"></bean>
```

> 注意名称中使用$字符将嵌套类名与外部类名分开.

