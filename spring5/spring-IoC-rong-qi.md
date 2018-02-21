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

> bean名称以小写字母开头, 使用骆驼为基础.





