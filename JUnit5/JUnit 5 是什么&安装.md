# JUnit 5 是什么?

JUnit 5跟以前的JUnit版本不一样, 它由几大不同的模块组成, 这些模块分别来自三个不同的子项目.

> 注意 是由三个模块组成了JUnit 5.
> 分别是:  JUnit Platform JUnit Jupiter JUnit Vintage

## JUnit Platform
是在JVM上 启动测试框架 的基础平台. 它还定义了 TestEngine API, 该API可用于开发在平台上运行的测试框架.
此外, 平台还提供了一个从命令行或者 Gradle 和 Maven 插件来启动的 控制台启动器, 它就好比一个基于JUnit 4的Runner 在平台上运行任何TestEngine.

## JUnit Jupiter
是一个组合体, 它是在JUnit 5中编写测试和扩展的新 编程模型 和 扩展模型 组成.
另外, Jupiter子项目还提供了一个TestEngine, 用于在平台上运行基于Jupiter的测试.

## JUnit Vintage
提供了一个TestEngine, 用于在平台上运行基于JUnit 3和JUnit 4的测试.

# 安装
## 依赖元数据
### JUnit Platform
Group ID: ```org.junit.platform```
Version: ```1.0.3```
Artifact IDs: 

**junit-platform-commons**
JUnit 内部通用类库/实用工具, 它们仅用于JUnit框架本身, 不支持任何外部使用.

**junit-platform-console**
支持从控制台中发现和执行JUnit Platform上的测试.

**junit-platform-console-standalone**
一个包含了Maven仓库中的 [junit-platform-console-standalone](https://repo1.maven.org/maven2/org/junit/platform/junit-platform-console-standalone/) 目录下所有依赖项的可执行JAR包.

**junit-platform-engine**
测试引擎的公共API

**junit-platform-gradle-plugin**
支持用Gradle在JUnit Platform上发现和执行测试.

**junit-platform-launcher**
配置和加载测试计划的公共API – 典型的使用场景是IDE和构建工具.

**junit-platform-runner**
在一个JUnit 4环境中的JUnit Platform上执行测试和测试套件的运行器

**junit-platform-suite-api**
在JUnit Platform上配置测试套件的注解。被 JUnit Platform运行器 所支持，也有可能被第三方的TestEngine实现所支持.

**junit-platform-surefire-provider**
支持使用 Maven Surefire 来发现和执行JUnit Platform上的测试.

### JUnit Jupiter
Group ID: ```org.junit.jupiter```
Version: ```5.0.3```
Artifact IDs:

**junit-jupiter-api**
编写测试 和 扩展 的JUnit Jupiter API.

**junit-jupiter-engine**
JUnit Jupiter测试引擎的实现, 仅仅在运行时需要.

**junit-jupiter-params**
支持JUnit Jupiter中的 参数化测试

**junit-jupiter-migration-support**
支持从JUnit 4迁移到JUnit Jupiter, 仅在使用了JUnit 4规则的测试中才需要.

### JUnit Vintage
Group ID: ```org.junit.vintage```
Version: ```4.12.3```
Artifact ID:

**junit-vintage-engine**
JUnit Vintage测试引擎实现, 允许在新的JUnit Platform上运行低版本的JUnit测试, 即那些以JUnit 3或JUnit 4风格编写的测试.

## 依赖
以上所有artifacts在它们已发布的Maven POM中都依赖了下面的 ```@API Guardian```  JAR文件.
Group ID: ```org.apiguardian```
Artifact ID: ```apiguardian-api```
Version: ```1.0.0```

此外, 上面大部分artifacts都对下面的OpenTest4J JAR文件有直接或传递的依赖关系
Group ID: ```org.opentest4j```
Artifact ID: ```opentest4j```
Version: ```1.0.0```
