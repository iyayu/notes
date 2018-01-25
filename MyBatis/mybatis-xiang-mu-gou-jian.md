# MyBatis 项目构建

## 入门

注意我写这篇笔记的时候 MyBatis 版本是 `3.4.6-SNAPSHOT` .

## 从 XML 中构建 SqlSessionFactory

每个基于 MyBatis 的应用都是以一个 `SqlSessionFactory` 的实例为中心的.`SqlSessionFactory`的实例可以通过`SqlSessionFactoryBuilder`获得. 而`SqlSessionFactoryBuilder`则可以从 XML 配置文件或一个预先定制的`Configuration`的实例 构建出`SqlSessionFactory`的实例.

从 XML 文件中构建`SqlSessionFactory`的实例非常简单, 建议使用类路径下的资源文件进行配置. 但是也可以使用任意的输入流\( InputStream\)实例, 包括字符串形式的文件路径或者 file:// 的 URL 形式的文件路径来配置.

MyBatis 包含一个名叫`Resources`的工具类, 它包含一些实用方法, 可使从`classpath`或其他位置加载资源文件更加容易.

```
String resource = "org/mybatis/example/mybatis-config.xml";
InputStream inputStream = Resources.getResourceAsStream(resource);
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
```

我们可以通过 [SqlSessionFactoryBuilder API](http://www.mybatis.org/mybatis-3/zh/apidocs/org/apache/ibatis/session/SqlSessionFactoryBuilder.html) 来查看都可以传递哪些参数来构建`SqlSessionFactory`实例.

> 注意: SqlSessionFactoryBuilder 这个类可以被实例化、使用和丢弃, 一旦创建了 SqlSessionFactory, 就不再需要它了.
>
> 因此 SqlSessionFactoryBuilder 实例的最佳作用域是方法作用域\(也就是局部方法变量\)

## 从 SqlSessionFactory 中获取 SqlSession

顾名思义, 我们有了`SqlSessionFactory`就可以从中获得`SqlSession`的实例了.

`SqlSession`完全包含了面向数据库执行 SQL 命令所需的所有方法. 你可以通过`SqlSession`实例来直接执行已映射的 SQL 语句.

执行已映射的SQL语句有两种方式, 我推荐您使用第一种方式.

因为第一方式执行清晰类型安全. 不用担心字符串字面值出错和强制类型转换问题.

**第一种方式, SQL 映射器**

```
SqlSession session = sqlSessionFactory.openSession();
try {
    BlogMapper mapper = session.getMapper(BlogMapper.class);
    Blog blog = mapper.selectBlog(101);
} finally {
    session.close();
}
```

**第二种方式, 使用 SqlSession 实例方法**

```
SqlSession session = sqlSessionFactory.openSession();
try {
    Blog blog = (Blog) session.selectOne("org.mybatis.example.BlogMapper.selectBlog", 101);
} finally {
    session.close();
}
```

你可以通过 [SqlSession API](http://www.mybatis.org/mybatis-3/zh/apidocs/org/apache/ibatis/session/SqlSession.html) 和 [SqlSessionFactory API](http://www.mybatis.org/mybatis-3/zh/apidocs/org/apache/ibatis/session/SqlSessionFactory.html) 来查看获取`SqlSession`中的方法和`SqlSession`实例.

> 注意: SqlSessionFactory 一旦被创建就应该在应用的运行期间一直存在, 没有必要对它进行清除或重建. 并且 SqlSessionFactory 只创建一次就可以了. 因此 SqlSessionFactory 的最佳作用域是应用作用域. 有很多方法可以做到, 最简单的就是使用单例模式或者静态单例模式.
>
> SqlSession 的实例不是线程安全的, 每个线程都应该有它自己的 SqlSession 实例. 所以它的最佳的作用域是请求或方法作用域. 换句话说, 每次收到的 HTTP 请求, 就打开一个 SqlSession, 返回一个响应, 就关闭它. 这个关闭操作是很重要的, 你应该把这个关闭操作 放到 finally 块中以确保每次都能执行关闭.

## SQL Mapper

在上面我们说过执行映射的 SQL 语句有两种方法, 推荐使用第一种方法. 而第一种方法就是我们说的 SQL Mapper\(SQL 映射器\).

它是由 Java 接口和 XML 文件\(注解\) 构成的, 而映射器的主要作用就是用来绑定映射语句和负责发送 SQL 去执行, 并返回结果.

映射器接口的实例是从`SqlSession`中获得的. 因此从技术层面讲, 映射器实例的最大作用域是和`SqlSession`相同的, 因为它们都是从 `SqlSession`里被请求的.

也就是说, 映射器实例应该在调用它们的方法中被请求, 用过之后即可废弃. 并不需要显式地关闭映射器实例.

下面的示例就展示了这个实践:

```
SqlSession session = sqlSessionFactory.openSession();
try {
    BlogMapper mapper = session.getMapper(BlogMapper.class);
    // do work
} finally {
    session.close();
}
```

> BlogMapper 类是一个接口, 我们不需要自己去实现, 当我们调用 SqlSession 实例的 getMapper 方法时, SqlSession 会帮我们自动实现这个接口.

## 探究已映射的 SQL 语句

这里给出一个基于 XML 映射语句的示例, 它应该可以满足上述示例中`SqlSession`的调用.

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="org.mybatis.example.BlogMapper">
    <select id="selectBlog" resultType="Blog">
        select * from Blog where id = #{id}
    </select>
</mapper>
```

上面代码看似很多, 但是我们将 XML 头部和文档类型声明占用的部分去掉再来看

```
<mapper namespace="org.mybatis.example.BlogMapper">
    <select id="selectBlog" resultType="Blog">
        select * from Blog where id = #{id}
    </select>
</mapper>
```

那么这个 XML 文件到底做了什么呢?

* 定义了命名空间为 `org.mybatis.example.BlogMapper` 的 SQL Mapper, 这个命名空间和我们定义接口的全限定名保持一致.
* 用 `select` 元素定义了一个查询 SQL, id 为 `selectBlog` , 和我们接口的方法名是一致的, `resultType` 定义返回类型.
* `#{id}` 是这条 SQL 语句的参数. 类似于 `?` 占位符. 你也可以使用 `${id}` 来拼接 SQL.

接下来就是获取 SQL Mapper, 执行我们的映射语句, 代码如下:

```
BlogMapper mapper = session.getMapper(BlogMapper.class);
Blog blog = mapper.selectBlog(101);
```

> 在默认情况下我们将 BlogMapper 类要和 BlogMapper.xml 文件在同一个包下, 也就是 org.mybatis.example 包下.

当然如果你觉得每次都要多写一个 XML 文件很麻烦, 那么我们可以通过注解来解决这个问题

```
package org.mybatis.example;
public interface BlogMapper {
    @Select("SELECT * FROM blog WHERE id = #{id}")
    Blog selectBlog(int id);
}
```

注解使代码显得更加简洁, 然而 Java 注解对于稍微复杂的语句就会力不从心并且会显得更加混乱. 因此, 如果你需要做很复杂的事情, 那么最好使用 XML 来映射语句.

## 要注意的问题

### mapper 的 namespace 属性

在前面的 XML 文件当中我们可以看到, `namespace` 属性值是包名+类名的形式.

而这个 `namespace` 值你可以写成上面的那种形式, 你也可以只写一个简单的值\(例如: BlogMapper\), 但是你要注意, 如果 `BlogMapper` 是唯一的就可以直接使用, 如果出现两个或两个以上的相同名称\(例如:`org.mybatis.example.BlogMapper` 和 `org.mybatis.example1.BlogMapper`\),  那么使用时就会跑出异常, 这种情况下就必须使用完全限定名.

这里推荐使用全限定名也就是包名+类名的形式, 这样可以直接拿来使用. 而 `namespace` 的作用就是与你的接口进行绑定. 

因为在MyBatis当中, 映射器实例是通过动态代理来实现的. 我们要告诉MyBatis你要帮我们动态代理哪个接口, 然后映射语句会和对应的方法名进行绑定, 从而生成映射器实例.
