# MyBatis 使用场景

## 批量更新
在数据库中使用批量更新有助于提高性能. 在 MyBatis 中, 我们可以修改配置文件中的 ```settings``` 的 ```defaultExecutorType``` 来制定其执行器为批量执行器.
```
<settings>
  <setting name="defaultExecutorType" value="BATCH"/>
</settings>
```

当然我们也可以用 Java 代码来实现批量执行器的使用.
```
sqlSessionFactory.openSession(ExecutorType.BATCH);
```

执行器分类
 - SIMPLE 就是普通的执行器(默认).
 - REUSE 执行器会重用预处理语句(prepared statements).
 - BATCH 执行器将重用语句并执行批量更新.
 
## 分表
在大型公司中, 账单表可能有上亿条数据, 对于这种情况我们往往需要进行分表处理.

我们可以吧2015年的账单保存在2015年的账单表中, 将2016年的账单保存在2016年的账单表中.

我们可以将年份当作参数传递到 SQL 语句中.
```
select * from t_bill_${year} where id = #{id}
```

注意 ```${year}``` 作用是将 ```year``` 的值直接拼接到 SQL 语句中, 注意这样做很危险的, 容易出现 SQL 注入.

