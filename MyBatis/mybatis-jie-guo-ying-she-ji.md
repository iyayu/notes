# MyBatis 结果映射集

resultMap 元素里面还有一下元素
```
    <resultMap id="BaseResultMap" type="cc.iyayu.basis.model.SystemUserAccountDO">
        <constructor>
            <idArg/>
            <arg/>
        </constructor>
        <id/>
        <result/>
        <association/>
        <collection/>
        <discriminator javaType="">
            <case />
        </discriminator>
    </resultMap>
```

其中 ```constructor``` 元素用于配置构造方法. 如果一个 POJO 没有无参构造, 我们可以使用此元素进行配置.
```idArg``` 元素标记结果作为 ID 可以帮助提高整体效能.
```arg``` 元素注入到构造方法的一个普通结果.

**id 元素和 result 元素**
```id``` 元素是表示哪个列是主键, 允许多个主键, 多个主键称为联合主键. ```result``` 是配置POJO 到 SQL 列名的映射关系.
```property``` 将结果映射到指定的属性.
```column``` 指定 SQL 的列.
```javaType``` 配置java的类型
```jdbcType``` 配置数据库类型
```typeHandler``` 类型处理器.

下面给出一个映射到 POJO 的例子:
```
<resultMap id="userResultMap" type="User">
  <id property="id" column="user_id" />
  <result property="username" column="user_name"/>
  <result property="password" column="hashed_password"/>
</resultMap>
```

# 级联
```association``` 代表一对一关系.

```collection``` 代表一对多关系.

```discriminator``` 是鉴别器

## ```association``` 一对一关系
### 嵌套查询
实际操作中, 首先我们需要确定对象的关系. 以学生信息级联为例, 在学校里面学生和学生证是一对一的关系.

因此, 我们建立一个 ```StudentBean``` 和 ```StudentSelfardBean``` 的 POJO 对象. 那么在 ```Student``` 的 POJO 我们就应该有一个类型为 ```StudentSelfardBean``` 的属性 ```studentSelfard```, 这样便形成了级联.

这时候我们需要建立 ```Student``` 的映射器 ```StudentMapper``` 和 ```StudentSelfcard``` 的映射器 ```StudentSelfcardMapper```.

而在 ```StudentSelfcardMapper``` 里面我们提供了一个 ```findStudentSelfcardByStudentId``` 的方法.
```
<select id="findStudentSelfcardByStudentId" parameterType="int" resultMap="StudentSelfcardMap">
    select * from id = #{studentId}
</select>
```

有了以上代码, 我们将可以在 ```StudentMapper``` 里面使用了 ```StudentSelfcardMapper``` 进行级联.

```
<association property="studentSelfcard" column="id" select="org.mybatis.example.StudentSelfcardMapper.findStudentSelfcardByStudentId"/>
```

```property="studentSelfcard"``` 指定哪个属性是联合的对象. 

```column="id"``` 则是指定传递给 ```select``` 语句的参数.

```select="org.mybatis.example.StudentSelfcardMapper.findStudentSelfcardByStudentId"``` 用指定的 SQL 去查询.

当取出 ```Student``` 的时候, MyBatis 就会使用下面 SQL 取出我们需要的级联信息.

```
org.mybatis.example.StudentSelfcardMapper.findStudentSelfcardByStudentId
```

也就是说我们将 ```column``` 列的值传递给 ```select``` 的映射 SQL 语句中, 然后将结果赋值给 ```property``` 设置的属性中.

> 如果有多个参数我们可以使用逗号分割, 例如: ```column="{prop1=col1,prop2=col2}"```

### 嵌套结果
如果我们的关联查询无法自动赋值, 这个时候我们就需要指定属性和类名来进行映射.

```
<resultMap id="blogResult" type="Blog">
  <id property="id" column="blog_id" />
  <result property="title" column="blog_title"/>
  <association property="author" column="blog_author_id" javaType="Author" resultMap="authorResult"/>
</resultMap>

<resultMap id="authorResult" type="Author">
  <id property="id" column="author_id"/>
  <result property="username" column="author_username"/>
  <result property="password" column="author_password"/>
  <result property="email" column="author_email"/>
  <result property="bio" column="author_bio"/>
</resultMap>
```

## ```collection``` 一对多关系
一对多和一对一是非常相似的, 这里用官网的例子说明
```
<resultMap id="blogResult" type="Blog">
  <collection property="posts" javaType="ArrayList" column="id" ofType="Post" select="selectPostsForBlog"/>
</resultMap>

<select id="selectBlog" resultMap="blogResult">
  SELECT * FROM BLOG WHERE ID = #{id}
</select>

<select id="selectPostsForBlog" resultType="Post">
  SELECT * FROM POST WHERE BLOG_ID = #{id}
</select>
```

首先当我们执行 ```selectBlog``` 这条映射语句的时候, 会返回一个 ```resultMap="blogResult"``` 的结果集.

在这个结果集中使用 ```collection``` 元素代表我关联的另一方是多个. 其它的几个属性和 ```association``` 元素中是一样的. 就是多了一个 ```ofType``` 属性.

```ofType``` 属性就是指定集合的泛型是什么类型.

对于查询嵌套结果来说与一对一是一样的.
```
<resultMap id="blogResult" type="Blog">
  <id property="id" column="blog_id" />
  <result property="title" column="blog_title"/>
  <collection property="posts" ofType="Post">
    <id property="id" column="post_id"/>
    <result property="subject" column="post_subject"/>
    <result property="body" column="post_body"/>
  </collection>
</resultMap>
```

## ```discriminator``` 鉴别器

先看看官网是怎么说的

> 一个单独的数据库查询有时候会返多种不同数据类型的结果集. 

也就是说我们可以通过鉴别器来将查询的结果映射为不同的对象, 例如: ```Vehicle``` 有两个子类分别为 ```Car``` 和 ```Boat```

其中父类的属性是两个子类中共有的属性, 而两个子类中有分别有它们自己特有的属性, 例如:

**Vehicle**
```
public class Vehicle {
    private int id;  
    private String vin;
    private Date year;  
    private String color;  
    private String vendor;  
    private int vehicleType;  
}
```

**Car**
```
public class Car extends Vehicle {
    private int doorCount;
}
```

**Boat**
```
public class Boat extends Vehicle {  
    private String quant;
}  
```

**你的表可能是这个样子的**
```
create table vehicle (  
 id bigint(10) primary key AUTO_INCREMENT,  
 vin varchar(10),  
 year date,  
 color varchar(10),  
 vendor varchar(10),  
 vehicle_type int,    //类型：1表示car, 2表示boat  
 door_count int,      //车门数量，car独有属性  
 quant varchar(10)    //船桨，boat独有属性  
);  
```

那么鉴别器是怎么区分我的某条记录属于 ```Car``` 还是 ```Boat```. 当然是通过 ```vehicle_type``` 字段了.

我们看一下在 XML 中如果来使用鉴别器
```
<mapper namespace="com.yjq.entity.Vehicle">  
    <resultMap id="vehicleResult" type="Vehicle">  
        <id property="id" column="id" />  
        <result property="vin" column="vin"/>  
        <result property="year" column="year"/>  
        <result property="vendor" column="vendor"/>  
        <result property="color" column="color"/>  
        <result property="vehicleType" column="vehicle_type"/>  
        
        <discriminator javaType="int" column="vehicle_type">  
            <case value="1" resultMap="carResult"/>  
            <case value="2" resultMap="boatResult"/>  
        </discriminator>  
    </resultMap>
    
    <resultMap id="carResult" type="Car">
        <result property="doorCount" column="door_count" />
    </resultMap>
    
    <resultMap id="boatResult" type="Boat">
        <result property="quant" column="quant" />
    </resultMap>  
      
    <select id="selectVehicle" parameterType="int" resultMap="vehicleResult">
        select * from vehicle where id = #{id};
    </select>
</mapper>  
```

```discriminator``` 元素表示鉴别器, ```javaType="int"``` 指定参数类型也就是 ```value="1"``` 的数据类型. ```column="vehicle_type"``` 就是我要取哪一列的值进行比较.

如果我们第一条记录的 ```vehicle_type``` 字段值为 ```1```, 那么采用 ```carResult```. 如果第二条记录 ```vehicle_type``` 字段值为 ```2``` 那么就会采用 ```boatResult```. 

> 如果 ```value``` 属性中没有```vehicle_type``` 的值, 则不会选取任何resultMap.

这是在一张表的情况下, 如果是多张表的情况下呢?
```
<resultMap id="femaleEmployeeMap" type="femaleEmployee">  
    <collection property="uterusList" select="com.xxxxx.xxxx.mapper.FemaleEmployeeMapper.findUterusList" column="id" />  
</resultMap>
```

上面我们看到一个繁琐的过程就是要创建多个 ```resultMap``` 元素, 我们可以使用一下方式来解决.
```
<resultMap id="vehicleResult" type="Vehicle">  
    <id property="id" column="id" />  
    <result property="vin" column="vin"/>  
    <result property="year" column="year"/>  
    <result property="vendor" column="vendor"/>  
    <result property="color" column="color"/>  
    <result property="vehicleType" column="vehicle_type"/>  
    <discriminator javaType="int" column="vehicle_type">  
        <case value="1" resultMap="carResult" type="Car">  
            <result property="doorCount" column="door_count" />  
        </case>  
        <case value="2" resultMap="boatResult" >  
            <result property="quant" column="quant" />  
        </case>  
    </discriminator>  
</resultMap>  
```

> 这些都是结果映射, 如果你不指定任何结果, 那么 MyBatis 将会为你自动匹配列和属性.




