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










