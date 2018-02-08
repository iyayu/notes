# MyBatis 使用场景

## 批量更新
在数据库中使用批量更新有助于提高性能. 在 MyBatis 中, 我们可以修改配置文件中的 ```settings``` 的 ```defaultExecutorType``` 来制定其执行器为批量执行器.
