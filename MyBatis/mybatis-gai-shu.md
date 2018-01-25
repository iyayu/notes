# MyBatis 概述

## 什么是 MyBatis?

MyBatis 是一款优秀的持久层框架, 它支持定制化SQL、存储过程以及高级映射. MyBatis 避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集.

MyBatis 可以使用简单的 XML 或注解来配置和映射原生信息, 将接口和 Java 的 POJOs\(Plain Old Java Objects, 普通的 Java对象\)映射成数据库中的记录.

这里相信大家都使用过原生JDBC来操作过数据库, 你的代码可能是下面这个样子的:

```
    Goddess g = null;
    Connection con = DBUtil.getConnection();//首先拿到数据库的连接
    String sql = "" +
    "select * from imooc_goddess "+
    "where id=?";//参数用?表示，相当于占位符;
    //预编译sql语句
    PreparedStatement psmt = con.prepareStatement(sql);
    //先对应SQL语句，给SQL语句传递参数
    psmt.setInt(1, id);
    //执行SQL语句
    /*psmt.execute();*///execute()方法是执行更改数据库操作（包括新增、修改、删除）;executeQuery()是执行查询操作
    ResultSet rs = psmt.executeQuery();//返回一个结果集
    //遍历结果集
    while(rs.next()) {
        g = new Goddess();
        g.setId(rs.getInt("id"));
        g.setUserName(rs.getString("user_name"));
        g.setAge(rs.getInt("age"));
        g.setSex(rs.getInt("sex"));
        //rs.getDate("birthday")获得的是java.sql.Date类型。注意：java.sql.Date类型是j
        ava.util.Date类型的子集，所以这里不需要进行转换了。
        g.setBirthday(rs.getDate("birthday"));
        g.setEmail(rs.getString("email"));
        g.setMobile(rs.getString("mobile"));
        g.setCreateUser(rs.getString("create_user"));
        g.setCreateDate(rs.getDate("create_date"));
        g.setUpdateUser(rs.getString("update_user"));
        g.setUpdateDate(rs.getDate("update_date"));
        g.setIsDel(rs.getInt("isdel"));
    }
```

 这一部分代码就是用来根据 `imooc_goddess` 表的 `id` 进行查询, 然后遍历结果集, 手动封装成 `Goddess` 对象.

如果我们这样做, 那么就需要做大量的费时费力的操作, 所以 MyBatis 来帮我们解决了 JDBC 代码和手动设置参数以及获取结果集的问题.

> 上面部分代码摘抄自 [这篇文章](https://www.cnblogs.com/Qian123/p/5339164.html#_label4)

## XML 映射文件

 在这一章的最前面我们说过

> MyBatis 可以使用简单的 XML 或注解来配置和映射原生信息, 将接口和 Java 的 POJOs\(Plain Old Java Objects, 普通的 Java对象\) 映射成数据库中的记录.

我们已经知道了 MyBatis 框架可以将结果集自动封装成对象, 那么 MyBatis 是怎么知道, 表中的字段是和类中的哪个属性对应的呢?

它们的对应关系就是依靠 `XML 映射文件` . 当然在 MyBatis 中 XML 映射文件不只是做字段和属性的映射, 还有做接口中方法的映射, 当你调用方法后会执行你所编写的SQL语句.

> 注意: XML 映射文件 也被称为元数据.

##  ORM 映射

说道字段和属性的映射就不得不说一说 `ORM 映射` , 当然你可能在看这篇笔记之前已经在网上 了解过 MyBatis, 你也可能看到过类似的文章说: MyBatis 是基于ORM 映射的框架, 有的说它不是. 

那么不管它是不是基于ORM 映射的框架, 我们也应该对ORM有所了解. 

ORM: \(Object/Relation Mapping\): 对象/关系映射, 是一种设计模式. 几乎所有的程序里面, 都存在对象和关系数据库. 它们的对应关系如下:

| 类 | 表 |
| :--- | :--- |
| 对象 | 表中的一条记录 |
| 属性 | 表中的字段 |

比如Hibernate, 它就是实现了ORM的框架, Hibernate 完全可以通过对象关系模型实现对数据库的操作, 拥有完整的JavaBean 对象与数据库的映射结构来自动生成sql. 

而 MyBatis 仅有基本的字段映射, 对象数据以及对象实际关系仍然需要通过手写sql来实现和管理.

所以有的人也说 Hibernate 是全自动, 而 MyBatis 是半自动.



