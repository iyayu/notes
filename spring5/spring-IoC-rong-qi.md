# IoC容器

## Spring IoC容器介绍
IoC就是 **Inversion of Control** 的缩写, 字面意思就是控制反转, 也被称为依赖注入(DI). 

**那么啥是控制反转?**

在使用Ioc容器之前我们是自己使用 ```new``` 关键字来创建和管理对象.

使用Ioc容器之后我们只需要由容器帮我们创建和管理, 当我们想要某个对象的时候可以直接从容器中取出.

这就是IoC容器的作用, 帮我们创建和管理对象, 当我们要获取的对象依赖某个对象的时候Ioc容器也会帮我们注入.

```
public class A {

}

public class B {
	A a;
}
```

上面的例子中 ```B``` 对象依赖 ```A``` 对象才能正常使用, 这时我们只需要将两个对象放入容器中.

当我们需要 ```B``` 对象时, 容器就会帮我们自动注入一个 ```A``` 对象了. 当然我们要通过配置来告诉容器.


## Bean介绍
由 Spring IoC 容器管理的对象都称为 ```bean```.

```org.springframework.context.ApplicationContext``` 接口表示 Spring IoC 容器, 通过读取元数据来实例化, 配置和装配bean.

> 元数据可以用XML, Java注解或Java代码表示.

## ApplicationContext接口

在Spring中, 两个最基本最重要的包是 ```org.springframework.beans``` 和 ```org.springframework.context``` 这两个包是 Spring IoC 容器的基础.

```ApplicationContext``` 建立在 ```BeanFactory``` 之上, 并增加了其他的功能, 比如更容易同Spring AOP特性整合, 消息资源处理(用于国际化), 事件传递, 以声明的方式创建 ```ApplicationContext```.

简而言之, ```BeanFactory``` 提供了配置框架和基本的功能, 而 ```ApplicationContext``` 为它增加了更强的功能.

ApplicationContext 接口提供了几个实现. 如果你是在独立应用程序中, 可以使用 ```ClassPathXmlApplicationContext```  或 ```FileSystemXmlApplicationContext``` 实例来创建Ioc容器.


## 元数据
使用XML来配置元数据, 下面是基本结构.
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="..." class="...">
        
    </bean>

</beans>
```

```bean``` 元素表示一个对象.
```id``` 属性标识单个bean定义的字符串.
```class``` 属性定义了bean的类型, 使用全限定名.

我们可以通过上面的XML例子来让容器帮我们管理多个对象, 但是如果我们项目中有很多对象需要被管理, 那么就要在这一个文件中写很多 bean, 这样不利于我们自己维护.

所以在有些时候我们需要将 bean 分别放在多个XML文件中. 例子如下:

**服务层对象(services.xml)配置文件**
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="petStore" class="org.springframework.samples.jpetstore.services.PetStoreServiceImpl">
        <property name="accountDao" ref="accountDao"/>
        <property name="itemDao" ref="itemDao"/>
        <!-- additional collaborators and configuration for this bean go here -->
    </bean>
</beans>
```

**数据访问对象(daos.xml)文件**
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="accountDao"
        class="org.springframework.samples.jpetstore.dao.jpa.JpaAccountDao">
    </bean>

    <bean id="itemDao" class="org.springframework.samples.jpetstore.dao.jpa.JpaItemDao">
    </bean>
</beans>
```

服务层对象 ```PetStoreServiceImpl``` 依赖两个数据访问对象 ```JpaAccountDao``` ```JpaItemDao```.
```property name``` 元素引用 ```JavaBean``` 属性的名称, 该 ```ref``` 元素引用另一个 ```bean id``` 定义的名称.
```bean id``` 和 ```ref``` 元素之间的这种联系表达了协作对象之间的依赖关系.


有时候我们也可能使用 ```<import/>``` 元素来从另一个或多个文件加载bean定义.
```
<beans>
    <import resource="services.xml"/>
    <import resource="resources/messageSource.xml"/>
    <import resource="/resources/themeSource.xml"/>

    <bean id="bean1" class="..."/>
    <bean id="bean2" class="..."/>
</beans>
```

外部 bean 定义是从三个文件加载: ```services.xml```, ```messageSource.xml``` 和 ```themeSource.xml```. 


## 创建和使用容器

**使用Xml配置**
```
ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");

PetStoreService service = context.getBean("petStore", PetStoreService.class);

List<String> userList = service.getUsernameList();
```
使用该方法 ```T getBean(String name, Class<T> requiredType)``` 可以检索bean的实例.


## 在 Spring 5 中我们也可以使用以下方式来进行创建

**使用Groovy配置**
```
ApplicationContext context = new GenericGroovyApplicationContext("services.groovy", "daos.groovy");
```

最灵活的配置方式是使用 ```GenericApplicationContext```, 例如 ```XmlBeanDefinitionReader``` 用于XML文件
```
GenericApplicationContext context = new GenericApplicationContext();
new XmlBeanDefinitionReader(context).loadBeanDefinitions("services.xml", "daos.xml");
context.refresh();
```

或者使用```GroovyBeanDefinitionReader``` 用于Groovy文件
```
GenericApplicationContext context = new GenericApplicationContext();
new GroovyBeanDefinitionReader(context).loadBeanDefinitions("services.groovy", "daos.groovy");
context.refresh();
```

## bean命名

每个bean都有一个或多个标识, 这些标识符在容器内必须是唯一的. 但是如果它需要多个标识符, 额外的标识符可以被认为是别名.

在基于XML的配置元数据中, 可以使用id或name属性来指定bean标识符. 如果有多个别名可以在name属性中用逗号(,) 分号(;)或空格分隔.

**services.xml文件**
```
<bean id="test1" name="alias1, alias2; alias3 alias4" class="cc.iyayu.Test1"></bean>
```

**bean获取方式**
```
GenericApplicationContext context = new GenericApplicationContext();
new XmlBeanDefinitionReader(context).loadBeanDefinitions("services.xml");
context.refresh();

// 通过id属性 获取bean
Test1 test1 = context.getBean("test1", Test1.class);

// 通过name属性(别名) 获取bean
Test1 alias1 = context.getBean("alias1", Test1.class);
alias1.show();

Test1 alias2 = context.getBean("alias2", Test1.class);
alias2.show();

Test1 alias3 = context.getBean("alias3", Test1.class);
alias3.show();

Test1 alias4 = context.getBean("alias4", Test1.class);
alias4.show();
```

> bean名称以小写字母开头, 使用骆驼为基础.

**使用alias元素来指定bean别名**
```
<alias name="fromName" alias="toName"/>
```
```fromName```: 该属性指定一个Bean实例的标识名, 表示将会为该Bean指定别名.

```toName```: 指定一个别名.

例子
```
<bean id="test1" name="alias1" class="cc.iyayu.Test1"></bean>
<alias name="test1" alias="alias2"/>
<alias name="alias1" alias="alias3"/>
```

## BeanDefinition 对象
SpringIoC容器管理一个或多个bean. 这些bean是用您提供给容器的配置元数据创建的, 例如以XML ```<bean/>``` 定义的形式创建.

在容器本身中, 这些bean定义表示为 ```BeanDefinition``` 对象, 这些对象包含(除其他信息外)以下元数据:
 - 包限定类名: 定义bean的实际实现类.
 - bean行为配置元素: 它声明这个bean在容器的行为方式(范围、生命周期回调等等)
 - bean所需其他bean的引用: 这些引用称为依赖项.
 - 在新创建的对象中设置的其他配置: 例如, 在管理连接池的bean中使用的连接数或池的大小限制.
 












