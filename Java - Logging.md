# 日志🍀

## 概述

日志框架分为两种类型：

- 日志抽象层框架
- 日志具体实现框架

SpringBoot 的日志抽象层框架使用了 SLF4j，具体实现框架使用了 Logback



## 日志框架

SLF4J 不同于其他日志类库，与其它日志类库有很大的不同。SLF4J (Simple logging Facade for Java)不是一个真正的日志实现，而是一个抽象层，它允许你在后台使用任意一个日志类库。如果是在编写供内外部都可以使用的 API 或者通用类库，那么你不会希望使用你类库的客户端必须使用你选择的日志类库。

### 抽象层

- JCL（Jakarta Commons Logging）
- SLF4j（Simple Logging Facade for Java）
- jboss-logging

### 实现层

- JUL（java.util.logging）

- Log4j
- Logback
- Log4j2 



### Springboot日志

SLF4j、Log4j、Logback 出自同一个作者，Logback 是对 Log4j 的改进

Log4j2 是 apache 制作的日志框架，性能最好

Spring 选用 JCL

Springboot 的 spring-boot-starter-logging 选用了 SLF4J 和 Logback



# SEL4J

## 架构

SLF4J 是各种日志框架的抽象层，大部分实现层框架没有对 slf4j 进行实现，所以需要一个适配层作为中转

logback 和 slf4j-simple 针对 slf4j 进行了实现，不需要适配层。

- `slf4j` + `logback`
- `slf4j` + `slf4j-simple`

- `slf4j` + `slf4j-log412` + `log4j`



<img src="http://store.secretcamp.cn/uPic/concrete-bindings20210520100134162147609419IVs519IVs5.png" alt="concrete-bindings" style="zoom:67%;" />



## 适配层

### log4j

1. log4j 没有针对 slf4j 进行实现，需要引入适配层 `slf4j-log4j1`

   ```xml
   <dependency>
       <groupId>org.slf4j</groupId>
       <artifactId>slf4j-api</artifactId>
       <version>1.7.30</version>
   </dependency>
   <dependency>
       <groupId>org.slf4j</groupId>
       <artifactId>slf4j-log4j12</artifactId>
       <version>1.7.30</version>
   </dependency>
   <dependency>
       <groupId>log4j</groupId>
       <artifactId>log4j</artifactId>
       <version>1.2.16</version>
   </dependency>
   ```



### log4j2

```xml
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>1.7.30</version>
</dependency>
<!-- slf4j - log4j2 适配层 -->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j-impl</artifactId>
    <version>2.14.1</version>
</dependency>
<!-- log4j2 的两个核心包 -->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.14.1</version>
</dependency>
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-api</artifactId>
    <version>2.14.1</version>
</dependency>
```



## 使用方法

要同时引入日志的抽象层以及实现层

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class HelloWorld {
  public static void main(String[] args) {
    Logger logger = LoggerFactory.getLogger(HelloWorld.class);
    logger.info("Hello World");
  }
}
```



## 日志冲突

如果选定的日志框架为 `slf4j` + `logback`，但是某些框架中使用了其他日志，例如 spring 1.x 使用了 `commons-logging`，那么可以先排除掉 spring 中的 `commons-logging` ，使用一个 `jcl-over-slf4j` 去替换，`jcl-over-slf4j` 和 `commons-logging` 拥有完全相同的类和方法签名，但是具体实现已经被替换了。

1. 将系统中的其他日志框架排除
2. 用中间包来替换原有的日志框架
3. 导入 slf4j 的其他实现层框架



同样，`log4j-over-slf4j` 可以用于取代 log4j ，这样 log4j 接口输出的日志就会通过 `log4j-over-slf4j` 路由到 slf4J 上



## 默认日志配置



```properties
# 指定日志输出级别
logging.level.kzq.yorua.springboot=trace

# 如果都指定，logging.file 优先生效
# 指定完整的路径和文件名，不指定路径在当前项目下生成springboot.log日志
logging.file=/Users/yorua/Project/IdeaProject/SpringBoot/springboot.log
# 指定路径，使用spring.log作为默认文件名
logging.path=/Users/yorua/Project/IdeaProject/SpringBoot

# 控制台输出的日志的格式
logging.pattern.console=%d{yyyy-MM-dd} [%thread] %-5level %logger{50} - %msg%n
# 日志文件中输出的格式
logging.pattern.file=%d{yyyy-MM-dd} === [%thread] === %-5level === %logger{50} ==== %msg%n
```



| logging.file | logging.path | Description                          |
| ------------ | ------------ | ------------------------------------ |
| (none)       | (none)       | 只在控制台输出                       |
| 指定文件名   | (none)       | 输出日志到指定文件                   |
| (none)       | 指定目录     | 输出到指定目录的 `spring.log` 文件中 |



## 指定日志配置

给类路径下放上每个日志框架自己的配置文件即可；SpringBoot就不使用他默认配置的了

| 日志框架                | Customization                                                |
| ----------------------- | ------------------------------------------------------------ |
| Logback                 | `logback-spring.xml`、`logback-spring.groovy`、 `logback.xml` 、 `logback.groovy` |
| Log4j2                  | `log4j2-spring.xml` 、 `log4j2.xml`                          |
| JDK (Java Util Logging) | `logging.properties`                                         |

日志配置文件识别机制：

logback.xml：直接就被日志框架识别

logback-spring.xml：日志框架不能直接识别，由 Springboot 解析日志配置，可以使用 Springboot 的高级 Profile 功能

```xml
<springProfile name="dev">
    <pattern> ... </pattern>
</springProfile>
<springProfile name="!dev">
     <pattern> ... </pattern>
</springProfile>
```



