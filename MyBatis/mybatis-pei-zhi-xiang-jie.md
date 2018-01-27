# MyBatis 配置详解

## MyBatis 配置 XML 文件层次结构
```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration> <!-- 配置 -->
    <properties/><!-- 属性 -->
    <settings/><!-- 设置 -->
    <typeAliases/><!-- 类型命名 -->
    <typeHandlers/><!-- 类型处理器 -->
    <objectFactory/><!-- 对象工厂 -->
    <plugins/><!-- 插件 -->
    <environments><!-- 配置环境 -->
        <environment><!-- 环境变量 -->
            <transactionManager type=""/><!-- 事物管理器-->
            <dataSource/><!-- 数据源 -->
        </environment>
    </environments>
    <databaseIdProvider/><!-- 数据库厂商标识 -->
    <mappers/><!-- 映射器 -->
</configuration>
```

> 这些层次最好不要颠倒顺序. 如果颠倒可能会出现异常.

## properties
properties 是一个配置属性的元素, 而且这些属性都是可外部配置且可动态替换的.
```
<properties resource="org/mybatis/example/config.properties">
  <property name="username" value="dev_user"/>
  <property name="password" value="F2Fa3!33TYyg"/>
</properties>
```

这样我们就可以在上下文中使用已经配置好的属性值了

```
<dataSource type="POOLED">
  <property name="driver" value="${driver}"/>
  <property name="url" value="${url}"/>
  <property name="username" value="${username}"/>
  <property name="password" value="${password}"/>
</dataSource>
```

## 优先级
如果属性在不只一个地方进行了配置, 那么 MyBatis 将按照下面的顺序来加载:
 - 在 ```properties``` 元素体内指定的属性首先被读取.
 - 然后根据 ```properties``` 元素中的 ```resource``` 属性读取类路径下属性文件或根据 ```url``` 属性指定的路径读取属性文件, 并覆盖已读取的同名属性.
 - 最后读取作为方法参数传递的属性, 并覆盖已读取的同名属性.

因此, 通过方法参数传递的属性具有最高优先级，```resource/url``` 属性中指定的配置文件次之, 最低优先级的是 ```properties``` 属性中指定的属性.

从MyBatis 3.4.2开始, 你可以为占位符指定一个默认值
```
<dataSource type="POOLED">
  <!-- ... -->
  <property name="username" value="${username:ut_user}"/>
</dataSource>
```

这个特性默认是关闭的. 如果你想为占位符指定一个默认值, 你应该添加一个指定的属性来开启这个特性. 例如:
```
<properties resource="org/mybatis/example/config.properties">
  <!-- ... -->
  <property name="org.apache.ibatis.parsing.PropertyParser.enable-default-value" value="true"/>
</properties>
```

## settings

这里直接提供[官网的资料](http://www.mybatis.org/mybatis-3/zh/configuration.html#settings).

## 别名

别名就是另一个名字. 因为我们用到的是权限定名, 这样每次都要这样写就太麻烦了. 就像下面的代码一样 ```resultType``` 使用的就是权限顶名

```
<mapper namespace="org.mybatis.example.BlogMapper">
    <select id="selectBlog" resultType="org.mybatis.example.Blog">
        select * from Blog where id = #{id}
    </select>
</mapper>
```

如果在多个地方都是用到了这个类型, 那么我们没每用到一次就要这样写一次很麻烦, 所以我们可以使用一个简短的名称去代替它. 这个简短的名称就是别名.

```
<mapper namespace="org.mybatis.example.BlogMapper">
    <select id="selectBlog" resultType="Blog">
        select * from Blog where id = #{id}
    </select>
</mapper>
```

一个别名(typeAliases) 的实例是在解析配置文件时候生成的, 然后一直保存在 ```Configuration``` 对象.

当我们使用到它的时候, 再取出来. 这样就没有必要在每次用到的时候再次生成它的实例了.

> 在 MyBatis 中别名是不分大小写的, 而且别名可以在 MyBatis 上下文中使用. 
> 别名在 MyBatis 里分为系统定义别名和自定别名两类. 



