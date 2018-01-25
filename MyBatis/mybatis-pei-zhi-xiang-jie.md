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

