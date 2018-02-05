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

```discriminator``` 是鉴别器, 使用结果的值来决定使用哪个结果映射. 比如, 人有男人和女人. 你可以实例化一个人的对象, 但是要根据情况用男人类或女人类去实例化.

## ```association``` 一对一关系
### 嵌套结果
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





