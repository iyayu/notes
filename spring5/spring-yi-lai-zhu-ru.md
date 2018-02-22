# 依赖注入

依赖注入(DI)是一个过程, 通过这个过程, 注入它们的依赖项.

也就是说, 对象可以通过构造函数参数, 工厂方法参数或者在返回对象实例后通过属性来设置它们的依赖关系.然后, 容器在创建bean时注入这些依赖项.

简单来说, a依赖b, 但a不控制b的创建和销毁, 仅使用b, 那么b的控制权交给a之外处理. 这叫控制反转(IOC).
而a要依赖b, 必然要使用b的instance, 那么:
 - 通过a的构造, 把b传入.
 - 通过设置a的属性, 把b传入.
 
这个过程叫依赖注入(DI).

## 基于构造函数的依赖注入
我们要使用 ```<constructor-arg/>``` 元素来设置构造函数的依赖
```
package x.y;

public class Foo {
    public Foo(Bar bar, Baz baz) {

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

如果 ```Bar``` 和 ```Baz``` 没有继承别的类, 所以类型是已知的. 我们不需要在 ```<constructor-arg/>``` 元素中明确指定构造函数参数索引和类型.

**构造函数参数类型匹配**
```
<bean id="exampleBean" class="examples.ExampleBean">
    <constructor-arg type="int" value="7500000"/>
    <constructor-arg type="java.lang.String" value="42"/>
</bean>
```

```
package examples;

public class ExampleBean {

    // Number of years to calculate the Ultimate Answer
    private int years;

    // The Answer to Life, the Universe, and Everything
    private String ultimateAnswer;

    public ExampleBean(int years, String ultimateAnswer) {
        this.years = years;
        this.ultimateAnswer = ultimateAnswer;
    }
}
```

**构造函数参数索引(默认)**
```
<bean id="exampleBean" class="examples.ExampleBean">
    <constructor-arg index="0" value="7500000"/>
    <constructor-arg index="1" value="42"/>
</bean>
```

**构造函数参数名称**
```
<bean id="exampleBean" class="examples.ExampleBean">
    <constructor-arg name="years" value="7500000"/>
    <constructor-arg name="ultimateAnswer" value="42"/>
</bean>
```

也可以使用 ```@ConstructorProperties``` JDK注释显式命名构造函数参数.
```
package examples;

public class ExampleBean {
    @ConstructorProperties({"years", "ultimateAnswer"})
    public ExampleBean(int years, String ultimateAnswer) {
        this.years = years;
        this.ultimateAnswer = ultimateAnswer;
    }
}
```

**调用一个static工厂方法来返回对象的一个实例**
```
<bean id="exampleBean" class="examples.ExampleBean" factory-method="createInstance">
    <constructor-arg ref="anotherExampleBean"/>
    <constructor-arg ref="yetAnotherBean"/>
    <constructor-arg value="1"/>
</bean>

<bean id="anotherExampleBean" class="examples.AnotherBean"/>
<bean id="yetAnotherBean" class="examples.YetAnotherBean"/>
```

```
public class ExampleBean {

    // a private constructor
    private ExampleBean(...) {
        ...
    }

    public static ExampleBean createInstance (
        AnotherBean anotherBean, YetAnotherBean yetAnotherBean, int i) {

        ExampleBean eb = new ExampleBean (...);

        return eb;
    }
}
```
```static``` 工厂方法的参数是通过 ```<constructor-arg/>``` 元素提供的, 就像实际使用构造函数一样. 工厂方法返回的类的类型不必与包含 ```static``` 工厂方法的类的类型相同, 尽管在本例中它是.

> 实例(非静态)工厂方法将以基本相同的方式使用(除了使用 ```factory-bean``` 属性而不是class属性)



## 基于Setter的依赖注入
基于Setter是通过调用无参数构造函数或无参数 ```static``` 工厂方法来实例化bean之后, 在bean上调用setter方法来完成的.
```
<bean id="exampleBean" class="examples.ExampleBean">
    <property name="beanOne"><ref bean="anotherExampleBean"/></property>
    <property name="beanTwo"><ref bean="yetAnotherBean"/></property>
    <property name="integerProperty"><value>1</value></property>
</bean>

<bean id="anotherExampleBean" class="examples.AnotherBean"/>
<bean id="yetAnotherBean" class="examples.YetAnotherBean"/>
```

```
public class ExampleBean {
    
    private AnotherBean beanOne;
    private YetAnotherBean beanTwo;
    private int i;
    
    public void setBeanOne(AnotherBean beanOne) {
        this.beanOne = beanOne;
    }
    
    public void setBeanTwo(YetAnotherBean beanTwo) {
        this.beanTwo = beanTwo;
    }
    
    public void setIntegerProperty(int i) {
        this.i = i;
    }    
}
```

## 依赖解决过程
Bean依赖的解决通常取决于下面这些内容:
 - 通过描述所有bean的配置元数据创建和初始化 ```ApplicationContext```. 配置元数据可以通过XML、Java代码或注释指定.
 - 对于每个bean, 如果使用的是属性、构造函数参数或静态工厂方法的参数, 而不是普通的构造函数. 当bean实际创建时, 这些依赖项将提供给bean.
 - 每个属性或构造函数参数都要设置的值是实际定义, 或者是对容器中另一个bean的引用.
 - 每个属性或构造函数参数都是一个值, 从其指定的格式转换为该属性或构造函数参数的实际类型. 默认情况下, Spring可以将以字符串格式提供的值转换为所有内置类型, 如int、long、string、boole等.
 

## 直接值(基础类型, String等等)
```<property/>``` 元素的 ```value``` 属性, 将对象中的属性或构造函数参数指定为可读的字符串表示形式. Spring的转换服务用于将这些值从字符串转换为属性或参数的实际类型.
```
<bean id="myDataSource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
    <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
    <property name="url" value="jdbc:mysql://localhost:3306/mydb"/>
    <property name="username" value="root"/>
    <property name="password" value="masterkaoli"/>
</bean>
```

> ```value="root"``` 中的 **root** 就是可读的字符串形式.

下面的示例使用p命名空间进行更简洁的XML配置.
```
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="myDataSource" class="org.apache.commons.dbcp.BasicDataSource"
        destroy-method="close"
        p:driverClassName="com.mysql.jdbc.Driver"
        p:url="jdbc:mysql://localhost:3306/mydb"
        p:username="root"
        p:password="masterkaoli"/>

</beans>
```

你也可以配置为 ```java.util.Properties``` 实例:
```
<bean id="mappings"
    class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">

    <property name="properties">
        <value>
            jdbc.driver.className=com.mysql.jdbc.Driver
            jdbc.url=jdbc:mysql://localhost:3306/mydb
        </value>
    </property>
</bean>
```

## idref元素
```idref``` 元素只是将容器中另一个bean的id(String值-而不是引用)传递给 ```<constructor-arg/>``` 或 ```<properties/>``` 元素的一种防止错误的方法.
```
<bean id="theTargetBean" class="..."/>

<bean id="theClientBean" class="...">
    <property name="targetName">
        <idref bean="theTargetBean"/>
    </property>
</bean>
```

上面的bean定义片段与下一段代码完全相同(在运行时):
```
<bean id="theTargetBean" class="..." />

<bean id="client" class="...">
    <property name="targetName" value="theTargetBean"/>
</bean>
```

第一种形式比第二种形式更好的原因是: 使用 ```idref``` 标记将会使Spring在部署的时候就验证其他的bean是否真正存在; 在第二种形式中, ```targetName``` 属性的类仅仅在Spring实例化这个类的时候做它自己的验证, 这很可能在容器真正部署完很久之后.

## 对其他bean的引用
```ref``` 元素是 ```<constructor-arg/>``` 或 ```<properties/>``` 定义元素中的最后一个元素. 在这里, 将bean的指定属性值设置为对容器管理的另一个bean(协作者)的引用.









