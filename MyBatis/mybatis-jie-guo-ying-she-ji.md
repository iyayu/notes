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













