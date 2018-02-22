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

通过 ```<ref/>``` 标记的bean属性指定目标bean是最常见的形式, 并且允许在同一个容器或父容器中创建对任何bean的引用, 而不管它是否位于同一个XML文件中. bean属性的值可以与目标bean的 ```id``` 属性相同，也可以是目标bean的 ```name``` 属性中的值之一.

```
<ref bean="someBean"/>
```

通过父属性指定目标bean将创建对当前容器父容器中bean的引用. 父属性的值可能与目标bean的 ```id``` 属性相同, 也可能与目标bean的 ```name``` 属性中的值相同, 并且目标bean必须位于当前bean的父容器中. 使用此bean引用主要是当您有一个容器的层次结构时, 并且您希望在子容器中使用与父bean同名的代理来包装现有的bean.
```
<!-- 父容器中 -->
<bean id="accountService" class="com.foo.SimpleAccountService">

</bean>
```

```
<!-- 子容器中 -->
<bean id="accountService"
    class="org.springframework.aop.framework.ProxyFactoryBean">
    <property name="target">
        <ref parent="accountService"/> <!-- 请注意我们如何引用父bean -->
    </property>
</bean>
```

## 内部beans
```<property/>``` 或 ```<constructor-arg/>``` 元素中的 ```<bean/>``` 元素定义了一个所谓的内部bean.
```
<bean id="outer" class="...">
    <property name="target">
        <bean class="com.example.Person">
            <property name="name" value="Fiona Apple"/>
            <property name="age" value="25"/>
        </bean>
    </property>
</bean>
```
内部bean定义不需要定义的 ```id``` 或 ```name```. 如果指定, 容器将不使用此类值作为标识符. 
容器还忽略了创建时的 ```scope``` 标志: 
 - 内部bean总是匿名的, 并且它们是用外部bean创建的.
 - 不能将内部bean注入到其他bean中, 也不可能独立地访问它们.
 
## 集合
在 ```<list/>```、```<set/>```、```<map/>``` 和 ```<props/>``` 元素中, 分别设置了Java集合类型 ```List```, ```Set```, ```Map```, ```Properties```

```
<bean id="moreComplexObject" class="example.ComplexObject">
    <!-- results in a setAdminEmails(java.util.Properties) call -->
    <property name="adminEmails">
        <props>
            <prop key="administrator">administrator@example.org</prop>
            <prop key="support">support@example.org</prop>
            <prop key="development">development@example.org</prop>
        </props>
    </property>
    <!-- results in a setSomeList(java.util.List) call -->
    <property name="someList">
        <list>
            <value>a list element followed by a reference</value>
            <ref bean="myDataSource" />
        </list>
    </property>
    <!-- results in a setSomeMap(java.util.Map) call -->
    <property name="someMap">
        <map>
            <entry key="an entry" value="just some string"/>
            <entry key ="a ref" value-ref="myDataSource"/>
        </map>
    </property>
    <!-- results in a setSomeSet(java.util.Set) call -->
    <property name="someSet">
        <set>
            <value>just some string</value>
            <ref bean="myDataSource" />
        </set>
    </property>
</bean>
```

设置键或值可以是以下元素
```
bean | ref | idref | list | set | map | props | value | null
```

## 集合合并
Spring容器还支持集合的合并. 应用程序开发人员可以定义父样式 ```<list/>```、```<map/>```、```<set/>``` 或 ```<props/>``` 元素, 并具有子样式 ```<list/>```、```<map/>```、```<set/>``` 或 ```<props/>``` 元素继承和重写父集合的值. 

也就是说, 子集合的值是合并父集合和子集合的元素的结果, 其子集合的集合元素覆盖父集合中指定的值.
```
<beans>
    <bean id="parent" abstract="true" class="example.ComplexObject">
        <property name="adminEmails">
            <props>
                <prop key="administrator">administrator@example.com</prop>
                <prop key="support">support@example.com</prop>
            </props>
        </property>
    </bean>
    
    <bean id="child" parent="parent">
        <property name="adminEmails">
            <props merge="true">
                <prop key="sales">sales@example.com</prop>
                <prop key="support">support@example.co.uk</prop>
            </props>
        </property>
    </bean>
<beans>
```

注意, 在 ```child``` bean中定义的 ```adminEmails``` 属性的 ```<props/>``` 元素上使用了 ```Merge=true``` 属性. 当容器解析并实例化 ```child``` bean时, 会创建一个 ```adminEmails``` 集合的实例, 该集合包含子实例 ```adminEmails``` 集合和父实例 ```adminEmails``` 集合合并后的结果.

```
administrator=administrator@example.com
sales=sales@example.com
support=support@example.co.uk
```

> ```key="support"``` 被替换成了 ```support@example.co.uk``` 所以子集合的集合元素覆盖父集合中指定的值.

**集合合并的局限性**
如果尝试合并不同的集合类型(例如Map和List), 就会引发适当的异常.

## 强类型集合
在Java 5中引入泛型类型后, 可以使用强类型集合. 也就是说, 可以声明 ```Collection``` 类型, 使其只能包含 ```String``` 元素. 
```
public class Foo {

    private Map<String, Float> accounts;

    public void setAccounts(Map<String, Float> accounts) {
        this.accounts = accounts;
    }
}
```

```
<beans>
    <bean id="foo" class="x.y.Foo">
        <property name="accounts">
            <map>
                <entry key="one" value="9.99"/>
                <entry key="two" value="2.75"/>
                <entry key="six" value="3.99"/>
            </map>
        </property>
    </bean>
</beans>
```
这是利用Spring的类型转换, 在元素添加到集合之前将其转换为适当的类型.

## Null 和 empty string 值
```
<bean class="ExampleBean">
    <property name="email" value=""/>
</bean>
```
同于以下Java代码
```
exampleBean.setEmail("");
```

```<null/>``` 元素处理null值
```
<bean class="ExampleBean">
    <property name="email">
        <null/>
    </property>
</bean>
```
同于以下Java代码
```
exampleBean.setEmail(null);
```

## 混合属性名
在设置bean属性时, 可以使用混合或嵌套属性名称.
```
<bean id="foo" class="foo.Bar">
    <property name="fred.bob.sammy" value="123" />
</bean>
```

## depends-on属性
有些时候我们要按照顺序来初始化bean, 在XML元数据配置文件中默认的是按照 ```<bean/>``` 元素的顺序进行初始化的.

但是在有些情况下我们可以使用 ```depends-on``` 属性来表示, 当前bean初始化之前要先初始化哪个bean.
```
<bean id="beanOne" class="ExampleBean" depends-on="manager"/>
<bean id="manager" class="ManagerBean" />
```
```
<bean id="beanOne" class="ExampleBean" depends-on="manager,accountDao">
    <property name="manager" ref="manager" />
</bean>

<bean id="manager" class="ManagerBean" />
<bean id="accountDao" class="x.y.jdbc.JdbcAccountDao" />
```

## 延迟加载(惰性初始化) beans
一个延迟初始化的bean告诉IoC容器在第一次请求时创建一个bean实例, 而不是在容器创建时.
```
<bean id="lazy" class="com.foo.ExpensiveToCreateBean" lazy-init="true"/>
<bean name="not.lazy" class="com.foo.AnotherBean"/>
```
使用 ```lazy-init``` 属性.

## 自动装配
通过检查 ```ApplicationContext``` 的内容, 您可以允许Spring自动创建bean之间的关系. 自动装配具有以下优点:
 - 减少我们显示的指定属性或构造函数参数.
 - 自动装配可以随着对象的变化而更新配置. 例如, 需要向类添加依赖项, 则可以自动满足该依赖关系, 而无需修改配置.
 
当使用基于XML配置的元数据时, 可以使用 ```<bean/>``` 元素的 ```autowire``` 属性为bean定义指定自动装配模式. 

自动装配功能有四种模式:
 - **no**: 不使用自动装配. Bean的依赖必须通过 ```ref``` 元素定义. 这是默认的配置, 在较大的部署环境中不鼓励改变这个配置, 因为明确的指定能够得到更多的控制和清晰性. 从某种程度上说, 这也是系统结构的文档形式.
 - **byName**: 通过属性名字进行自动装配. 这个选项会会检查BeanFactory，查找一个与将要装配的属性同样名字的bean 。比如，你有一个bean的定义被设置为通过名字自动装配，它包含一个master属性（也就是说，它有一个setMaster(...)方法），Spring就会查找一个叫做master的bean定义，然后用它来设置master属性。
 - **byType**: 如果BeanFactory中正好有一个同属性类型一样的bean，就自动装配这个属性。如果有多于一个这样的bean，就抛出一个致命异常，它指出你可能不能对那个bean使用byType的自动装配。如果没有匹配的bean，则什么都不会发生，属性不会被设置。如果这是你不想要的情况（什么都不发生），通过设置dependency-check="objects"属性值来指定在这种情况下应该抛出错误。
 - **constructor**: 这个同byType类似，不过是应用于构造函数的参数。如果在BeanFactory中不是恰好有一个bean与构造函数参数相同类型，则一个致命的错误会产生。












