# Springboot🍀

Spring Boot 是由 Pivotal 团队提供的全新框架，其设计目的是用来简化新 Spring 应用的初始搭建以及开发过程。该框架使用了特定的方式来进行配置，从而使开发人员不再需要定义样板化的配置。



## 参考文档

👉  [Springboot 官方文档](https://docs.spring.io/spring-boot/docs/current/reference/html/)

👉  [Springboot API Doc](https://docs.spring.io/spring-boot/docs/current/api/)



## Springboot的优点

1. 快速创建独立运行的 Spring 项目以及与主流框架集成

2. 使用嵌入式的 Servlet 容器，应用无需打成 WAR 包  

3. starters 自动依赖与版本控制

4. 大量的自动配置，简化开发，也可修改默认值

5. 无需配置 XML，无代码生成，开箱即用

6. 准生产环境的运行时应用监控

7. 与云计算的天然集成



## QuickStart

1. 使用 `Spring Initialize ` 构建一个新的项目

2. 选择依赖：web -> web spring

   选择依赖实际上是 `Spring Initialize ` 为我们自动导入了 maven 的相关依赖

   
   
3. 项目构建后，会自动生成一个主程序类

   ```java
   @SpringBootApplication  // 标注一个主程序类
   public class SpringbootHelloworldApplication {
   
       public static void main(String[] args) {
           // 启动spring
           SpringApplication.run(SpringbootHelloworldApplication.class, args);
       }
   
   }
   ```

4. 创建一个 Controller

   ```java
   @Controller
   public class HelloController {
       @ResponseBody
       @RequestMapping("/hello")
       public String hello() {
           return "Hello Spring Boot!";
       }
   }
   ```

   

5. 从主程序启动项目

   ```zsh
   mvn spring-boot:run
   ```

   

6. 将项目打成可执行jar包并部署

   ```shell
   mvn clean package
   java -jar target/springboot-helloworld-0.0.1-SNAPSHOT.jar
   ```

   必须要引入相关插件，否则运行 jar 包时会提示：jar中没有主清单属性

   ```xml
   <build>
       <plugins>
           <plugin>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-maven-plugin</artifactId>
           </plugin>
       </plugins>
   </build>
   ```

   



## pom.xml配置项

### 父项目

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.4.0</version>
    <relativePath/> 
</parent>
```



### 依赖

spring-boot-starter 指 springboot 场景启动器，帮我们导入了 web 模块正常运行所依赖的组件

Springboot将所有的功能场景都抽取出来，封装成 [starter](https://docs.spring.io/spring-boot/docs/current/reference/html/using.html#using.build-systems.starters) ，只需要在项目里面引入 starter，相关场景的所有依赖都会导入进来。

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```



# SpringApplication🍀

## 启动

`SpringApplication` 类提供了一种方便的方式来引导从 `main()` 方法启动的 Spring 应用程序，可以委托给静态的`SpringApplication.run()` 方法。

```java
@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }

}
```

Spring 程序启动时，INFO 级别的日志会输出在控制台，也可以通过 `spring.main.log-startup-info` 关闭 Spring 启动时的日志输出



## 自定义banner

可以将 banner.txt 文件放置在 classpath 路径下，除此之外，还支持 jpg、png、gif 格式。

也可以在配置文件中指定 `spring.banner.image.location`





## CommandLineRunner

如果希望在 SpringApplication 启动时就运行一些代码，可以实现 `CommandLineRunner` 或者 `ApplicationRunner` 接口，这两个接口都提供一个  `run()`  方法，在 `SpringApplication.run(…)` 完成后被调用。





# 常用注解🍀

## 配置相关注解🌵

### @ConfigurationProperties

`@ConfigurationProperties`：告诉 Springboot 将本类中的所有属性和配置文件中相关的配置进行绑定

```java
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface ConfigurationProperties {
    @AliasFor("prefix")
    String value() default "";

    @AliasFor("value")
    String prefix() default "";

    boolean ignoreInvalidFields() default false;

    boolean ignoreUnknownFields() default true;
}
```



默认从全局配置中注入数据，只有容器中的组件，才能享受到容器提供的 `@ConfigurationProperties` 功能

注解配置类：

```java
@Component
@Data
@ConfigurationProperties(prefix="database")
public class MyDatabase {
    private String name;
    private Integer port;
    private List<String> tables;
    private Map<String, Object> maps;
}
```

配置文件：

```yaml
database:
  name: mysql-dev
  port: 3306
  tables:
    - user
    - account
    - article
  maps:
    mirror: aliyun
    connect: 5
```

注入之后的 bean-id 为首字母小写的类名：myDatabase



对于有传递依赖的类也可以注入：

```java
@Data
public class MyDog {
    private String name;
    private Integer age;
}


@Component
@Data
@ConfigurationProperties(prefix = "person")
public class MyPerson {
    private Integer id;
    private String name;
    private Integer age;
    private MyDog dog;
    // ...
}
```

配置文件：

```yaml
person:
  id: ${random.int}
  name: Tom ${person.alias:TT}
  age: ${random.int(20,25)}
  dog:
    name: ${person.name}'s Rabbit
    age: 5
```





### @EnableConfigurationProperties

作用：令使用 `@ConfigurationProperties` 注解的类生效。

对于引入第三方依赖的类，无法修改其源码，自然无法在类上添加 `@Component` 之类的注解将其加入容器，此时就需要用到 `@EnableConfigurationProperties` 。





此时 `HelloServiceProperties` 并没有被注入容器，所以 `@EnableConfigurationProperties` 可以将 `HelloServiceProperties` 注入容器。

```java
@ConfigurationProperties(prefix = "service.properties")
@Data
public class HelloServiceProperties {
    private static final String SERVICE_NAME = "test-service";

    private String msg = SERVICE_NAME;

}


@Configuration
@EnableConfigurationProperties(HelloServiceProperties.class)
@ConditionalOnClass(HelloService.class)
@ConditionalOnProperty(prefix = "hello", value = "enable", matchIfMissing = true)
public class HelloServiceAutoConfiguration {

}

@RestController
public class ConfigurationPropertiesController {

    @Autowired
    private HelloServiceProperties helloServiceProperties;

    @RequestMapping("/getObjectProperties")
    public Object getObjectProperties () {
        System.out.println(helloServiceProperties.getMsg());
        return myConfigTest.getProperties();
    }
}
```



以下情况通过 `@Controller` 已经将 `HelloServiceProperties` 注入了容器，那么 `HelloServiceAutoConfiguration`  就不需要再加 `@EnableConfigurationProperties` 注解了。

```java
@ConfigurationProperties(prefix = "service.properties")
@Component
public class HelloServiceProperties {
    private static final String SERVICE_NAME = "test-service";

    private String msg = SERVICE_NAME;

    public String getMsg() {
        return msg;
    }

    public void setMsg(String msg) {
        this.msg = msg;
    }

}
```



### @EnableAutoConfiguration







### @SpringBootApplication

该注解有以下效果：

- `@Configuration`：标记当前类是一个配置类
- `@EnableAutoConfiguration`: Tells Spring Boot to start adding beans based on classpath settings, other beans, and various property settings. For example, if `spring-webmvc` is on the classpath, this annotation flags the application as a web application and activates key behaviors, such as setting up a `DispatcherServlet`.
- `@ComponentScan`: 允许 Spring 扫描并寻找其他组件



### @JsonIgnoreProperties

忽略任何未被绑定的属性，即不在 javabean 中没有的属性（自然也不应该有set、get方法）



## 测试相关注解🌵

### @SpringBootTest



## 条件相关注解🌵

### @ConditionalOnProperty

只有拥有某个属性，才会加载组件。

只有在 `application.properties` 或 `application.yml` 匹配到了 `spring.aop.auto=true` 才会加载被标注的组件，`matchIfMissing` 表示即使没有匹配到，也会加载被标注的组件，默认值是 false 。

```java
@ConditionalOnProperty(prefix = "spring.aop", name = "auto", havingValue = "true", matchIfMissing = true)
```



### @ConditionalOnClass

判断某个类是否存在于 classpath 中



### ConditionalOnMissingBean

某个 Bean 存在于应用上下文时才会加载，相反还有 `@ConditionalOnMissingBean`



### @ConditionalOnSingleCandidate

只有工厂类已经在容器中，且该工厂类只有单个产品实例时才会加载。

例如：`redisConnectionFactory` 在容器中，同时也只有一个产品 `RedisTemplate`  类型位于容器中。



# SpringBoot配置🍀

Springboot 相关的所有配置文档：https://docs.spring.io/spring-boot/docs/current/reference/html/



## 配置解析相关依赖

SpringBoot使用一个全局的配置文件，配置文件名是固定的；

- `application.properties`
- `application.yaml`

可以使用 properties，也可以使用 yaml 作为配置文件

配置文件的作用：修改 SpringBoot 自动配置的默认值；

1. 在 `pom.xml` 中导入配置文件处理器，配置文件进行绑定就会有提示

```xml
<!-- 生成配置文件的元数据信息，配置文件绑定时会有提示 -->
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-configuration-processor</artifactId>
  <optional>true</optional>
</dependency>
```



2. 编写配置文件

   ```yaml
   # application.yaml
   
   database:
     name: mysql
     port: 3306
     tables:
       - user
       - account
       - article
     maps:
       mirror: aliyun
       connect: 5
   ```

   



## 配置文件占位符

1. 随机数

2. 获取之前配置的值
3. 获取之前配置的值，如果不存在则使用默认值

```yaml
person:
  id: ${random.int}
  name: Tom ${person.alias:TT}
  age: ${random.int(20,25)}
  dog:
    name: ${person.name}'s Rabbit
    age: 5
```



## 多环境配置文件

Profile是Spring对不同环境提供不同配置功能的支持，可以通过激活、指定参数等方式快速切换环境



### 多profile文件形式

格式： `application-{profile}.properties` 或 `application-{profile}.yaml`

优先级：

- 默认配置文件 `application.yaml` 任何时候都会被加载

- `spring.profiles.active` 指定当前环境，与默认配置文件同时生效
- 同名配置项，当前环境配置文件优先

```yaml
# application.yaml
spring:
  profiles:
    active: dev  # 当前激活的环境是dev
```

```yaml
# application.dev.yaml
database:
  name: mysql-dev
  port: 3306
  tables: { user, account, article }
  maps: { mirror: aliyun, connect:5 }
```

```yaml
# application.stage.yaml
database:
  name: mysql-stage
  port: 3307
  tables: { user, account, article }
  maps: { mirror: aliyun, connect:5 }
```



### 多profile文档块形式

```yaml
# application.yaml
spring:
  profiles:
    active: dev
    
---
server: 8080
spring:
  profiles:dev
  
---
server: 8081
spring:
  profiles:test
```



### 项目参数配置形式

- 命令行： `--spring profiles.active=dev` 

- 配置文件： `spring.profiles.active=dev` 

- jvm参数： `-Dspring.profiles.active=dev`



## 配置文件加载位置

springboot 启动会扫描以下位置的 `application.properties` 或 `application.yml` 文件作为 springboot 的默认配置文件

- classpath：项目目录
  - `/classpath`
  - `/classpath/config`

- current：当前目录

  - `/current`
  - `/current/config`

  

以上是按照 优先级从高到低 的顺序，所有位置的文件都会被加载，高优先级配置 内容会 覆盖低优先级配置 内容。

也可以在部署项目时手动指定配置路径，这时指定的配置中，与原配置相同信息的会覆盖，不同的信息会互补。

```sh
java -jar springboot-helloworld-0.0.1-SNAPSHOT.jar --spring.config.location=/path/to/profile
```



## 外部配置文件加载

SpringBoot也可以从以下位置加载配置，优先级从高到低；高优先级的配置覆盖低优先级的配置，所有配置会形成互补。

1. 默认配置

2. `@Configuration` 上的 `@PropertySource` 注解

3. 配置文件 `application.properties` 

   - jar包外部的 `application-{profile}.properties` 或 `application.yml` (带spring.profile) 配置文件

   - jar包内部的 `application-{profile}.properties` 或 `application.yml` (带spring.profile) 配置文件

   - jar包外部的 `application.properties` 或 `application.yml` (不带spring.profile)配置文件

   - jar包内部的 `application.properties` 或 `application.yml` (不带spring.profile)配置文件

4. `RandomValuePropertySource` 配置的 `random.*` 属性值

5. OS 环境变量

6. Java 系统属性 `System.getProperties()`

7. 来自 `java:comp/env` 的 JNDI 属性

8. 命令行参数

   ```sh
   java -jar target/springboot-helloworld-0.0.1-SNAPSHOT.jar --server.port=8088
   ```

   



# 自动配置原理🍀

## 核心思想

SpringBoot 定义了一套接口规范，这套规范规定：SpringBoot 在启动时会扫描外部引用 jar 包中的`META-INF/spring.factories`文件，将文件中配置的类型信息加载到 Spring 容器，并执行类中定义的各种操作。对于外部 jar 来说，只需要按照 SpringBoot 定义的标准，就能将自己的功能装置进 SpringBoot。



## 依赖管理

springboot 配置文件中的父项目是 `spring-boot-starter-parent`

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.4.1</version>
    <relativePath/>
</parent>
```



`spring-boot-starter-parent` 的父项目是 `spring-boot-dependencies`

```xml
  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>2.4.1</version>
  </parent>
```

在 `spring-boot-dependencies` 中，几乎声明了所有开发中常用的依赖的版本号



### 自动版本仲裁

自动版本仲裁是指在引入依赖时，无需关心版本号。

对于 `spring-boot-dependencies` 中配置过的依赖，可以应用自动版本仲裁，但是如果没有配置过，则必须在引入依赖时声明相关的版本。

例如引入 mysql 相关的依赖，可以不声明版本号

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
```

因为在 `spring-boot-dependencies` 的 `<properties>` 中已经声明过 mysql 的版本了

```xml
<mysql.version>8.0.22</mysql.version>
```

如果想要自定义 Springboot 已经声明过的版本，可以直接在 `pom.xml` 中设置相关属性，当然也可以直接在 `<dependency>` 中带上版本号。

```xml
<properties>
    <mysql.version>5.1.43</mysql.version>
</properties>
```



### 场景启动器

[starts](https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-starter) 被称为场景启动器

官方的 starters 格式为 `spring-boot-starter-*` 

非官方的 starters 格式为 `thirdpartyproject-spring-boot-starter`



## @SpringBootApplication

SpringBoot中可以配置的属性： [Application Properties](https://docs.spring.io/spring-boot/docs/current/reference/html/appendix-application-properties.html#common-application-properties)

`@SpringBootApplication` 的元注解：

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
public @interface SpringBootApplication {

}
```





### @SpringBootConfiguration

`@SpringBootConfiguration` 使用了 `@Configuration` ，即表示当前类是一个配置类

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Configuration
public @interface SpringBootConfiguration {
    @AliasFor(
        annotation = Configuration.class
    )
    boolean proxyBeanMethods() default true;
}
```





### @EnableAutoConfiguration

`@EnableAutoConfiguration` 是实现自动装配的核心注解，它是 `@AutoConfigurationPackage` 和 `@Import` 的合成

自动装配核心功能的实现实际是通过`@Import` 导入的 `AutoConfigurationImportSelector`类。

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage
@Import({AutoConfigurationImportSelector.class})
public @interface EnableAutoConfiguration {
    String ENABLED_OVERRIDE_PROPERTY = "spring.boot.enableautoconfiguration";

    Class<?>[] exclude() default {};

    String[] excludeName() default {};
}
```



#### @AutoConfigurationPackage

`@AutoConfigurationPackage` 用于将指定包下的所有组件注入容器

使用了 `@Import({Registrar.class})` 将 `Registrar` 中的相关方法返回值注入容器

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@Import({Registrar.class})
public @interface AutoConfigurationPackage {
    String[] basePackages() default {};

    Class<?>[] basePackageClasses() default {};
}
```

`Registrar` 类中有一个 `registerBeanDefinitions()` 方法，该方法调用了 `register()` 方法，用于给容器批量注入组件。

metadata 表示被注解类的元信息，而这个被注解类就是主程序类 `DemoApplication.class` ，这里通过 metadata  获取了`DemoApplication.class` 所在包的包名并传入 `register()` 方法，即将 `DemoApplication.class` （启动类）所在包下的所有组件注册到容器之中，即将用户程序代码中自定义的组件注入容器。

```java
public void registerBeanDefinitions(AnnotationMetadata metadata, BeanDefinitionRegistry registry) {
    AutoConfigurationPackages.register(registry, (String[])(new AutoConfigurationPackages.PackageImports(metadata)).getPackageNames().toArray(new String[0]));
}
```



#### AutoConfigurationImportSelector

`AutoConfigurationImportSelector` 中有一个 `selectImports()` 方法，该方法主要用于获取所有符合条件的类的全限定类名，将这些类被加载到 IOC 容器中。

该方法中的主要逻辑在于 `getAutoConfigurationEntry()` 方法，方法的值最终被转为 String[] 返回

```java
public String[] selectImports(AnnotationMetadata annotationMetadata) {
    if (!this.isEnabled(annotationMetadata)) {
        return NO_IMPORTS;
    } else {
        AutoConfigurationImportSelector.AutoConfigurationEntry autoConfigurationEntry = this.getAutoConfigurationEntry(annotationMetadata);
        return StringUtils.toStringArray(autoConfigurationEntry.getConfigurations());
    }
}
```

`getAutoConfigurationEntry()` 方法的核心在于 `getCandidateConfigurations()` 方法，用于获取候选的组件，得到候选的组件后，经过一系列排除、过滤操作后得到最终应该注入容器的组件。

> Springboot 2.4.5 可以获取 133 个候选组件

```java
protected AutoConfigurationImportSelector.AutoConfigurationEntry getAutoConfigurationEntry(AnnotationMetadata annotationMetadata) {
    // 1. 判断自动状态有没有打开，可在application.properties中配置
    if (!this.isEnabled(annotationMetadata)) {
        return EMPTY_ENTRY;
    } else {
        AnnotationAttributes attributes = this.getAttributes(annotationMetadata);
        // 获取候选配置
        List<String> configurations = this.getCandidateConfigurations(annotationMetadata, attributes);
        configurations = this.removeDuplicates(configurations);
        Set<String> exclusions = this.getExclusions(annotationMetadata, attributes);
        this.checkExcludedClasses(configurations, exclusions);
        configurations.removeAll(exclusions);
        configurations = this.getConfigurationClassFilter().filter(configurations);
        this.fireAutoConfigurationImportEvents(configurations, exclusions);
        return new AutoConfigurationImportSelector.AutoConfigurationEntry(configurations, exclusions);
    }
}
```



`getCandidateConfigurations()` 方法的核心在于 `loadFactoryNames()` 方法 

```java
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
    List<String> configurations = SpringFactoriesLoader.loadFactoryNames(this.getSpringFactoriesLoaderFactoryClass(), this.getBeanClassLoader());
    Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you are using a custom packaging, make sure that file is correct.");
    return configurations;
}
```



`loadFactoryNames()` 方法的核心在于 `loadSpringFactories()` 方法

```java
public static List<String> loadFactoryNames(Class<?> factoryType, @Nullable ClassLoader classLoader) {
    ClassLoader classLoaderToUse = classLoader;
    if (classLoader == null) {
        classLoaderToUse = SpringFactoriesLoader.class.getClassLoader();
    }

    String factoryTypeName = factoryType.getName();
    return (List)loadSpringFactories(classLoaderToUse).getOrDefault(factoryTypeName, Collections.emptyList());
}
```



在 `loadSpringFactories()` 方法中，最终需要从各包的 `"META-INF/spring.factories"` 路径下加载资源

```java
Enumeration urls = classLoader.getResources("META-INF/spring.factories");
```



例如 `spring-boot-autoconfigure-2.4.5.jar` 中，就存在 `META-INF/spring.factories` 文件，Springboot 就是读取了这些文件中的配置信息，在该配置文件中所有写明的自动配置类，都是即将被导入 IOC 容器的候选类。

需要注意的是，不是每个包都有 `META-INF/spring.factories` 文件

<img src="http://store.secretcamp.cn/uPic/image-20210512095751721202105120957521620784672Y52uPoY52uPo.png" alt="image-20210512095751721" style="zoom:56%;" />

<img src="http://store.secretcamp.cn/uPic/image-20210512100027389202105121000271620784827lGFTxslGFTxs.png" alt="image-20210512100027389" style="zoom: 33%;" />



### @ComponentScan





## XXXAutoConfiguration分析

取  `spring-boot-autoconfigure-2.4.5.jar`  中的几个类进行分析

### AopAutoConfiguration

以 AOP 的配置为例，`AopAutoConfiguration` 中有两个静态内部类，它们都是配置类：

- `ClassProxyingConfiguration`
- `AspectJAutoProxyingConfiguration`

```java
@Configuration(
    proxyBeanMethods = false
)
// 表示只有 spring.aop.auto=true 时，以下配置才生效
// matchIfMissing = true 表示即使不配置 spring.aop.auto=true ，也认为 spring.aop.auto=true
@ConditionalOnProperty(
    prefix = "spring.aop",
    name = {"auto"},
    havingValue = "true",
    matchIfMissing = true
)
public class AopAutoConfiguration {
    public AopAutoConfiguration() {
    }

    @Configuration(
        proxyBeanMethods = false
    )
    // 只有不存在 org.aspectj.weaver.Advice 类时，以下配置才生效
    // 意思是不能导入 aspectj 包
    @ConditionalOnMissingClass({"org.aspectj.weaver.Advice"})
  	// 表示只有 spring.aop.proxy-target-class=true 时，以下配置才生效
    // matchIfMissing 表示以上配置中的 true 是默认情况
    @ConditionalOnProperty(
        prefix = "spring.aop",
        name = {"proxy-target-class"},
        havingValue = "true",
        matchIfMissing = true
    )
    static class ClassProxyingConfiguration {
				// ...
    }

    @Configuration(
        proxyBeanMethods = false
    )
    // 只有存在 Advice 类时，以下配置才生效，而 Advice 是指 org.aspectj.weaver.Advice;
  	// 如果没有导入 aspectj 包，以下配置就不生效
    // 所以如果导入了 aspectj 包，就用下面的配置
    @ConditionalOnClass({Advice.class})  
    static class AspectJAutoProxyingConfiguration {
        // ...
    }
}

```



### DispatcherServletAutoConfiguration

```java
@AutoConfigureOrder(-2147483648)
@Configuration(
    proxyBeanMethods = false
)
@ConditionalOnWebApplication(
    type = Type.SERVLET
)
@ConditionalOnClass({DispatcherServlet.class})
@AutoConfigureAfter({ServletWebServerFactoryAutoConfiguration.class})
public class DispatcherServletAutoConfiguration {
    public static final String DEFAULT_DISPATCHER_SERVLET_BEAN_NAME = "dispatcherServlet";
    public static final String DEFAULT_DISPATCHER_SERVLET_REGISTRATION_BEAN_NAME = "dispatcherServletRegistration";

    public DispatcherServletAutoConfiguration() {
    }

    @Order(2147483637)
    private static class DispatcherServletRegistrationCondition extends SpringBootCondition {
				// ...
    }

    @Order(2147483637)
    private static class DefaultDispatcherServletCondition extends SpringBootCondition {
				// ...
    }

    @Configuration(
        proxyBeanMethods = false
    )
    @Conditional({DispatcherServletAutoConfiguration.DispatcherServletRegistrationCondition.class})
    @ConditionalOnClass({ServletRegistration.class})
    // 将 WebMvcProperties.class 与当前配置类绑定，同时注入容器
    @EnableConfigurationProperties({WebMvcProperties.class})
    @Import({DispatcherServletAutoConfiguration.DispatcherServletConfiguration.class})
    protected static class DispatcherServletRegistrationConfiguration {
				// ...
    }

    @Configuration(
        proxyBeanMethods = false
    )
    @Conditional({DispatcherServletAutoConfiguration.DefaultDispatcherServletCondition.class})
    @ConditionalOnClass({ServletRegistration.class})
    @EnableConfigurationProperties({WebMvcProperties.class})
    protected static class DispatcherServletConfiguration {
				// ...
    }
}
```





## XXXProperties分析





# Web开发🍀

## 静态资源处理

👉 [官方文档](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-spring-mvc-static-content)

SpringBoot 对静态资源的自动配置在 `Webmvcautoconfiquration` 类中

寻找静态资源时，会从以下路径查找

```
"classpath:/META‐INF/resources/"
"classpath:/resources/"
"classpath:/static/"
"classpath:/public/"
```

访问：当前项目的根路径 + 静态资源路径



### 资源路径

可以自定义静态资源的路径，但默认的静态资源路径都将不可用。

```properties
# application.properties
spring.web.resources.static-locations=classpath:/test/
```



### 访问路径

由于 Springboot2 之后静态资源也经过拦截器，所以需要额外配置

所以可以自定义静态资源的访问路径，在拦截器中配置白名单，以避免静态资源被拦截，

现在所有的静态资源都要经过 `/res/` 路由

```properties
# application.properties
spring.mvc.static-path-pattern=/res/**
```





## webjars

[webjars](https://www.webjars.org/) 是将客户端资源（JavaScript，Css等）打成jar包文件，通过maven等构件工具对资源进行统一依赖管理。



所有 `/webjars/**` 路径下的请求 ，会在 `classpath:/META-INF/resources/webjars/` 寻找资源

```
http://localhost:8080/webjars/jquery/3.5.1/jquery.js
```





## 欢迎页面

👉 [官方文档](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-spring-mvc-welcome-page)

配置了欢迎页面后，访问项目根路径会直接展示欢迎页面

- 欢迎页为所有静态文件路径下的 `index.html`
  - 不能配置 `spring.mvc.static-path-pattern` 否则欢迎页面会失效
- Controller 处理的 /index







## 自定义类型转换器

将字符串类型根据具体规则转变为 `Account` 类型

```java
@Configuration
public class WebConfig {
    @Bean
    public WebMvcConfigurer webMvcConfigurer() {
        return new WebMvcConfigurer() {
            @Override
            public void addFormatters(FormatterRegistry registry) {
                registry.addConverter(new Converter<String, Account>() {

                    @Override
                    public Account convert(String source) {
                        Account account = new Account();
                        String[] split = source.split("|");
                        account.setCardNumber(split[0]);
                        account.setCardNumber(split[1]);
                        return account;
                    }
                });
            }
        };
    }
}
```



## 拦截器🍀



### 放行静态资源

1. 配置每个静态资源的位置

   ```java
   @Configuration
   public class MyMvcConfig implements WebMvcConfigurer {
       @Override
       public void addInterceptors(InterceptorRegistry registry) {
           registry.addInterceptor(new LoginInterceptor())
                   .addPathPatterns("/**")  // 所有请求都被拦截，包括静态资源
                   .excludePathPatterns("/login", "/css/**", "/fonts/**", "/images/**", "/js/**");
       }
   }
   ```

   

2. 设置访问前缀，配置静态资源文件夹

   先设置静态资源访问前缀，对应的，所有静态资源必须通过前缀访问

   ```properties
   # application.properties
   spring.mvc.static-path-pattern=/static
   ```

   设置拦截器排除路径

   ```java
   @Configuration
   public class MyMvcConfig implements WebMvcConfigurer {
       @Override
       public void addInterceptors(InterceptorRegistry registry) {
           registry.addInterceptor(new LoginInterceptor())
                   .addPathPatterns("/**")  // 所有请求都被拦截，包括静态资源
                   .excludePathPatterns("/login", "/static/**);
       }
   }
   ```

   



# 视图解析与模板引擎🍀

Springboot 默认打包方式是 jar 包，而 JSP 不支持在 jar 包内编译，所以 Springboot 默认不支持 JSP





# 开发效率工具🍀

## Lombok

1. 在 maven 中引入 lombok 相关依赖

   ```xml
   <dependency>
       <groupId>org.projectlombok</groupId>
       <artifactId>lombok</artifactId>
   </dependency>
   ```

   

2. 还需要下载 Idea 的 lombok 插件



### 简化JavaBean开发



### 简化日志开发



## Developer Tools

👉  [官方文档](https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-devtools)

devtools 会监控所有 classpath 中的修改，并实现热重启

触发热重启：command + F9  构建项目

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <optional>true</optional>
</dependency>
```







## Configuration Processor





 



## Spring Initailizr

使用 Spring Initailizr 创建后工程的项目结构如下：

- mvnw：maven wrapper script，它可以在没有安装 maven 或者 maven 版本不兼容的条件下运行 maven 的命令

<img src="http://store.secretcamp.cn/uPic/image-20210516214427624202105162144271621172667FHOtNLFHOtNL.png" alt="image-20210516214427624" style="zoom:50%;" />





