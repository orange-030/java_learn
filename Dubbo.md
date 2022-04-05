# Dubbo🌀

Apache Dubbo 是一款高性能、轻量级的开源 Java RPC 框架，它提供了三大核心能力：面向接口的远程方法调用、智能容错和负载均衡，以及服务自动注册和发现。



## 参考文档

👉  [Dubbo 官方文档](https://dubbo.apache.org/zh/docs/v2.7/user/)

👉  [Dubbo Github](https://github.com/apache/dubbo)

👉  [Dubbo Springboot Project]()



## 架构演变

1. 单一应用架构

   当网站流量很小时，只需一个应用，将所有功能都部署在一起，以减少部署节点和成本。此时，用于简化增删改查工作量的数据访问框架（ORM）是关键。

2. 垂直应用架构

   当访问量逐渐增大，单一应用增加机器带来的加速度越来越小，将应用拆成互不相干的几个应用，以提升效率。此时，用于加速前端页面开发的 Web 框架（MVC）是关键。

3. 分布式服务架构

   当垂直应用越来越多，应用之间交互不可避免，将核心业务抽取出来，作为独立的服务，逐渐形成稳定的服务中心，使前端应用能更快速的响应多变的市场需求。此时，用于提高业务复用及整合的分布式服务框架 (RPC) 是关键。



## RPC的概念

RPC（Remote Procedure Call）是指远程过程调用，是一种进程间通信方式，是一种技术的思想，而不是规范。

它允许程序调用另一个地址空间（通常是共享网络的另一台机器上）的过程或函数，而不用程序员显式编码这个远程调用的细节。即程序员无论是调用本地的还是远程的函数，本质上编写的调用代码基本相同。



## RPC与HTTP

RPC 和 HTTP 并不是一个并行的概念，HTTP 是协议，RPC 指的是远程调用方案，它包括接口规范、序列化协议、通信协议，其中通信可以用各种协议实现，包括 HTTP 。

RPC 是远端过程调用，其调用协议通常包含传输协议和序列化协议。

- 传输协议：gRPC 使用的 http2 协议，dubbo使用自定义报文的 TCP 协议
- 序列化协议: 如基于文本编码的 xml、json，基于二进制编码的 protobuf、hessian

<img src="http://store.secretcamp.cn/uPic/image-202108151657500542021081516575016290178706dBVzU6dBVzU.png" alt="image-20210815165750054" style="zoom:50%;" />



基于自定义 TCP 协议的 RPC 和 HTTP 请求两者最大的区别自然在于传输协议。

HTTP 请求使用的标准 TCP 协议，与自定义的 TCP 协议在报文上有所区别，通用定义的 http 1.1 协议的 TCP 报文包含太多无效信息，一个 POST 协议的格式大致如下：

元数据也就是请求头使用了文本编码，非常占字节数，但是这些信息调用方其实并不关心。

```http
HTTP/1.0 200 OK 
Content-Type: text/plain
Content-Length: 137582
Expires: Thu, 05 Dec 1997 16:00:00 GMT
Last-Modified: Wed, 5 August 1996 15:55:28 GMT
Server: Apache 0.84

<html>
  <body>Hello World</body>
</html>
```



而自定义 TCP 协议的报文如下：

报文头部占用的字节数也就只有 16 个 byte，极大地精简了传输内容。

<img src="http://store.secretcamp.cn/uPic/image-20210815160634386202108151606341629014794rQ5N6krQ5N6k.png" alt="image-20210815160634386" style="zoom:30%;" />



HTTP 调用方式与 RPC 调用方式的区别如下：

-  HTTP 有用信息占比少，效率低；RPC 有用信息占比高，效率高。
- HTTP 可读性好，RPC 可读性差。
- HTTP 调用远程方法比较复杂，要封装各种参数名和参数值；RPC 可以使用原始参数类型调用。



## RPC的调用过程

服务 A 希望调用服务 B 内部的方法：

1. 调用本地假的实现

   服务 A 调用的是一个接口，就必须为接口构造一个假的实现，所以使用动态代理，调用方的调用就被动态代理接收到了。

2. 调用远程真正实现

   动态代理接收到调用后，要调用远程的实际实现

   - 识别具体要调用的远程方法的 IP、端口
   - 将调用方法的入参进行序列化
   - 通过通信将请求发送到远程的方法中

3. 服务方处理请求

   - 反序列化各个调用参数
   - 定位到实际要调用的方法，然后输入参数，执行方法
   - 按照调用的路径返回方法调用的结果

<img src="http://store.secretcamp.cn/uPic/image-202108151704366712021081517043616290182769CESBk9CESBk.png" alt="image-20210815170436671" style="zoom:50%;" />



## Dubbo解决的问题

1. 高性能、透明的 RPC 调用：Dubbo 可以让开发者像调用本地的方法一样调用远程服务
2. 服务的自动注册与发现
   - 自动负载与容错：Dubbo 提供了完整的集群容错机制 ，可以实现软件层面的负载均衡 ，以此降低硬 件的压力
   - 动态流量调度：Dubbo提供了管理控制台 ，用户可以在界面上动态地调整每个服务的权重 、路由规则、禁用/启用，实现运行时的流量调度 
   - 依赖分析与调用统计 ：Dubbo 可以使用独立监控中心来监控接口的调用次数及耗时，用户可以根据这些数据反推出系统容量 



## Dubbo的总体调用过程



## Dubbo基本架构

### 节点角色

| 节点      | 角色说明                                                     |
| --------- | ------------------------------------------------------------ |
| Provider  | 暴露服务的服务提供方，服务提供者在启动时，向注册中心注册自己提供的服务。 |
| Consumer  | 调用远程服务的服务消费方，服务消费者在启动时，向注册中心订阅自己所需的服务，服务消费者，从提供者地址列表中，基于软负载均衡算法，选一台提供者进行调用，如果调用失败，再选另一台调用。 |
| Registry  | 注册中心返回服务提供者地址列表给消费者，如果有变更，注册中心将基于长连接推送变更数据给消费者 |
| Monitor   | 服务消费者和提供者，在内存中累计调用次数和调用时间，定时每分钟发送一次统计数据到监控中心 |
| Container | 服务运行容器                                                 |





### 调用关系

1. 服务容器负责启动，加载，运行服务提供者。
2. 服务提供者在启动时，向注册中心注册自己提供的服务。
3. 服务消费者在启动时，向注册中心订阅自己所需的服务。
4. 注册中心返回服务提供者地址列表给消费者，如果有变更，注册中心将基于长连接推送变更数据给消费者。
5. 服务消费者，从提供者地址列表中，基于软负载均衡算法，选一台提供者进行调用，如果调用失败，再选另一台调用。
6. 服务消费者和提供者，在内存中累计调用次数和调用时间，定时每分钟发送一次统计数据到监控中心。

<img src="http://store.secretcamp.cn/uPic/image-20210522093600720202105220936001621647360xKWKeexKWKee.png" alt="image-20210522093600720" style="zoom:50%;" />







# Dubbo相关概念🌀

## 注册中心

Dubbo 支持五种[注册中心](https://dubbo.apache.org/zh/docs/v2.7/user/references/registry/)



## 服务发现

1. 启动：Provider 绑定指定端口并启动服务

2. 注册服务：Provider 连接注册中心，将本机 IP、端口、应用信息和提供服务信息发送至注册中心存储

3. 订阅服务：Consumer 连接注册中心 ，发送应用信息、所求服务信息至注册中心

4. 服务订阅或变更时，推送服务地址列表

   注册中心根据 Consumer 请求的服务信息匹配对应的 Provider 列表，并发送至 Consumer 应用缓存 ，Provider 状态变更会实时通知注册中心，然后注册中心实时推送至 Consumer

5. 调用服务：Consumer 在发起远程调用时，选择基于缓存的 Provider 列表中的一个 Provider 的地址，基于权重随机选择一个发起调用
   
6. Dubbo 后台定时采集服务调用次数和调用时间等信息




## 服务变更

服务变更依赖于 Zookeeper 的 Watch 机制。

Consumer 会向  Zookeeper 注册一个 watcher 监听某一节点，Zookeeper 会和 Provider 保持心跳（实际上建立的是一个 Socket 长连接），如果 Provider 失联，对应的 node 会被删除，或者新的 Provider 上线会请求 Zookeeper，在某一 serviceName 下建立一个新的节点。于是 /dubbo/serviceName 发生变动，触发 Watch 机制，消费者端刷新 Provider 信息。




# Dubbo配置🌀

## 配置优先级

第一优先级：JVM 的 -D 启动参数

第二优先级：xml 文件中的配置

第三优先级：dubbo.properties 中的配置





## XML配置

| 标签                   | 用途         | 解释                                                         |
| ---------------------- | ------------ | ------------------------------------------------------------ |
| `<dubbo:service/>`     | 服务配置     | 用于暴露一个服务，定义服务的元信息，一个服务可以用多个协议暴露，一个服务也可以注册到多个注册中心 |
| `<dubbo:reference/>`   | 引用配置     | 用于创建一个远程服务代理，一个引用可以指向多个注册中心       |
| `<dubbo:protocol/>`    | 协议配置     | 用于配置提供服务的协议信息，协议由提供方指定，消费方被动接受 |
| `<dubbo:application/>` | 应用配置     | 用于配置当前应用信息，不管该应用是提供者还是消费者           |
| `<dubbo:module/>`      | 模块配置     | 用于配置当前模块信息，可选                                   |
| `<dubbo:registry/>`    | 注册中心配置 | 用于配置连接注册中心相关信息                                 |
| `<dubbo:monitor/>`     | 监控中心配置 | 用于配置连接监控中心相关信息，可选                           |
| `<dubbo:provider/>`    | 提供方配置   | 当 ProtocolConfig 和 ServiceConfig 某属性没有配置时，采用此缺省值，可选 |
| `<dubbo:consumer/>`    | 消费方配置   | 当 ReferenceConfig 某属性没有配置时，采用此缺省值，可选      |
| `<dubbo:method/>`      | 方法配置     | 用于 ServiceConfig 和 ReferenceConfig 指定方法级的配置信息   |
| `<dubbo:argument/>`    | 参数配置     | 用于指定方法参数配置                                         |



## 动态配置中心

每个服务的配置都写在自己的配置文件中，更新配置时要一台机器一台机器地修改，很不方便





## 元数据中心

元数据中心用于存储一些服务提供者、消费者的信息，比如dubbo版本、服务接口的信息（包括方法名、形参表、返回值类型）等等。

## 属性配置

Dubbo 可以自动加载 classpath 根目录下的 `dubbo.properties`



## API配置



## 注解配置

### @EnableDubbo

`@EnableDubbo` 整合了两个注解：

- `@EnableDubboConfig`

  引入了 `DubboConfigConfigurationRegistrar` 类，用于解析配置相关的类注册到 spring 容器

- `@DubboComponentScan`

  引入了 `DubboComponentScanRegistrar` 类，用于指定 `@DubboService` 的扫描路径

 

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
@EnableDubboConfig
@DubboComponentScan
public @interface EnableDubbo {
    @AliasFor(
        annotation = DubboComponentScan.class,
        attribute = "basePackages"
    )
    String[] scanBasePackages() default {};

    @AliasFor(
        annotation = DubboComponentScan.class,
        attribute = "basePackageClasses"
    )
    Class<?>[] scanBasePackageClasses() default {};

    @AliasFor(
        annotation = EnableDubboConfig.class,
        attribute = "multiple"
    )
    boolean multipleConfig() default true;
}
```



### @DubboService

相当于 `<dubbo:service/>` 标签，也具有将标注的类加入 ioc 容器的作用。



### @DubboReference

相当于 `<dubbo:reference/>` 标签，将远程实现的 bean 加入 ioc 容器，同时又完成依赖注入的过程。



### @Method



# 最佳实践🌀

## 失败重试

失败自动切换，当出现失败，重试其它服务器，但重试会带来更长延迟。

```xml
<!-- 服务端配置-->
<dubbo:service retries="3" />


<!-- 消费端配置-->
<dubbo:reference retries="3" />

<dubbo:reference>
    <dubbo:method name="findFoo" retries="3" />
</dubbo:reference>
```



## 超时时间

由于网络或服务端不可靠，会导致调用出现一种不确定的中间状态（超时）。为了避免超时导致客户端资源（线程）挂起耗尽，必须设置超时时间。

1. 配置原则

   dubbo推荐在Provider上尽量多配置Consumer端属性：

   - 作为 Provider，比 Consumer 更清楚服务性能参数，如调用的超时时间，合理的重试次数
   - 在 Provider 配置后，Consumer不配置则会使用 Provider 的配置值，即Provider配置可以作为Consumer的缺省值。否则，Consumer 会使用 Consumer 端的全局设置，这对于 Provider 不可控的，并且往往是不合理的



2. 服务提供者

   ```xml
   <!-- 全局超时配置-->
   <dubbo:provider timeout="5000" />
   
   <!-- 指定接口以及特定方法超时配置 -->
   <dubbo:provider interface="com.foo.BarService" timeout="2000">
       <dubbo:method name="sayHello" timeout="3000" />
   </dubbo:provider>
   ```



3. 服务消费者

   ```xml
   <!-- 全局超时配置-->
   <dubbo:consumer timeout="5000" />
   
   <!-- 指定接口以及特定方法超时配置 -->
   <dubbo:reference interface="com.foo.BarService" timeout="2000">
       <dubbo:method name="sayHello" timeout="3000" />
   </dubbo:reference>
   ```

   

## 多版本

当一个接口实现，出现不兼容升级时，可以用版本号过渡，版本号不同的服务相互间不引用。

```xml
<!-- 老版本服务提供者配置 -->
<dubbo:service interface="com.foo.BarService" version="1.0.0" />

<!-- 新版本服务提供者配置 -->
<dubbo:service interface="com.foo.BarService" version="2.0.0" />

<!-- 老版本服务消费者配置 -->
<dubbo:reference id="barService" interface="com.foo.BarService" version="1.0.0" />

<!-- 新版本服务消费者配置 -->
<dubbo:reference id="barService" interface="com.foo.BarService" version="2.0.0" />

```



## 本地存根





# Dubbo高可用🌀

## 本地缓存通讯与直连

Zookeeper 作为注册中心如果宕机，服务消费者仍然可以调用 dubbo 暴露的服务

- 服务提供者和服务消费者仍能通过本地缓存通讯

- 服务提供者和服务消费者可以通过直连的方式通讯，不经过注册中心

```xml
<dubbo:reference id="xxxService" interface="com.alibaba.xxx.XxxService" url="dubbo://localhost:20890" />
```



## 负载均衡

在集群负载均衡时，Dubbo 提供了多种均衡策略，默认为 random 随机调用

- Random LoadBalance（按权重随机）
- RoundRobin LoadBalance（按权重轮询）
- LeastActive LoadBalance（最少活跃调用）
- ConsistentHash LoadBalance（一致性哈希）



## 集群容错

在集群调用失败时，Dubbo 提供了多种容错方案，默认为 Failover 重试。

- Failover Cluster：失败自动切换，当出现失败，重试其它服务器，可以设置重试次数。
- Failfast Cluster：快速失败，只发起一次调用，失败立即报错。
- Failsafe Cluster：失败安全，出现异常时，直接忽略。通常用于写入审计日志等操作。
- Failback Cluster：失败自动恢复，后台记录失败请求，定时重发，通常用于消息通知操作。
- Forking Cluster：并行调用多个服务器，只要一个成功即返回，通常用于实时性要求较高的读操作，但需要浪费更多服务资源。
- Broadcast Cluster：广播调用所有提供者，逐个调用，任意一台报错则报错，通常用于通知所有提供者更新缓存或日志等本地资源信息。



## 服务降级

当服务器压力剧增的情况下，根据实际业务情况及流量，对一些服务和页面有策略的不处理或换种简单的方式处理，从而释放服务器资源以保证核心交易正常运作或高效运作。



# 监控中心🌀

## Dubbo-Admin

github：https://github.com/apache/dubbo-admin



### dubbo-admin-ui

启动参考：https://www.cnblogs.com/yxth/p/11819874.html

修改前端配置文件 `dubbo-admin-ui/vue.config.js`

```
port: 8021  # 前端项目地址，即浏览器中要访问的端口

target: 'http://120.79.198.81:8082/'   # 后端项目地址
```

在 dubbo-admin-ui 中执行以下命令运行前端服务

```sh
npm install
npm run dev
```





### dubbo-admin-server

在 dubbo-admin-server/src/main/resources/application.properties 中修改配置文件

```properties
server.port=8082
admin.registry.address=zookeeper://120.79.198.81:2181?backup=120.79.198.81:2182,120.79.198.81:2183
admin.config-center=zookeeper://120.79.198.81:2181?backup=120.79.198.81:2182,120.79.198.81:2183
admin.metadata-report.address=zookeeper://120.79.198.81:2181?backup=120.79.198.81:2182,120.79.198.81:2183
```



在 dubbo-admin-server 中构建后端服务

```sh
mvn clean package -Dmaven.test.skip=true
```

然后运行构建好的 jar 包

```sh
java -jar target/dubbo-admin-server-0.3.0-SNAPSHOT.jar
```

或者

```sh
mvn --projects dubbo-admin-server spring-boot:run
```



## Dubbo-Monitor







# Dubbo原理🌀





# Dubbo-Spring🌀

## 项目构建

### 项目架构

```ruby
dubbo-learn-xml
     |-------- interface-api
     |-------- dubbo-provider-user
     |-------- dubbo-provider-order
     |-------- dubbo-consumer
```



### 依赖

1. 父项目

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <project xmlns="http://maven.apache.org/POM/4.0.0"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
       <modelVersion>4.0.0</modelVersion>
   
       <groupId>kzq.yorua</groupId>
       <artifactId>dubbo-learn-xml</artifactId>
       <packaging>pom</packaging>
       <version>1.0-SNAPSHOT</version>
       <modules>
           <module>dubbo-interface</module>
           <module>dubbo-provider-user</module>
           <module>dubbo-provider-order</module>
           <module>dubbo-consumer</module>
       </modules>
       <dependencies>
           <dependency>
               <groupId>org.springframework</groupId>
               <artifactId>spring-context</artifactId>
               <version>5.2.15.RELEASE</version>
           </dependency>
       </dependencies>
   </project>
   ```

   

2. 服务提供者

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <project xmlns="http://maven.apache.org/POM/4.0.0"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
       <parent>
           <artifactId>dubbo-learn-xml</artifactId>
           <groupId>kzq.yorua</groupId>
           <version>1.0-SNAPSHOT</version>
       </parent>
       <modelVersion>4.0.0</modelVersion>
   
       <artifactId>dubbo-provider-order</artifactId>
       <dependencies>
           <!-- API 接口-->
           <dependency>
               <groupId>kzq.yorua</groupId>
               <artifactId>interface-api</artifactId>
               <version>1.0-SNAPSHOT</version>
           </dependency>
           <!-- Dubbo-->
           <dependency>
               <groupId>org.apache.dubbo</groupId>
               <artifactId>dubbo</artifactId>
               <version>2.7.8</version>
           </dependency>
          <!-- Zookeeper-->
           <dependency>
               <groupId>org.apache.zookeeper</groupId>
               <artifactId>zookeeper</artifactId>
               <version>3.4.10</version>
           </dependency>       
           <dependency>
               <groupId>org.apache.curator</groupId>
               <artifactId>curator-framework</artifactId>
               <version>2.12.0</version>
           </dependency>
           <dependency>
               <groupId>org.apache.curator</groupId>
               <artifactId>curator-recipes</artifactId>
               <version>2.12.0</version>
           </dependency>
       </dependencies>
   
   </project>
   ```

   

3. 服务消费者

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <project xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xmlns="http://maven.apache.org/POM/4.0.0"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
       <parent>
           <artifactId>dubbo-learn-xml</artifactId>
           <groupId>kzq.yorua</groupId>
           <version>1.0-SNAPSHOT</version>
       </parent>
       <modelVersion>4.0.0</modelVersion>
   
       <artifactId>dubbo-consumer</artifactId>
       <dependencies>
           <!-- API 接口-->
           <dependency>
               <groupId>kzq.yorua</groupId>
               <artifactId>interface-api</artifactId>
               <version>1.0-SNAPSHOT</version>
           </dependency>
           <!-- Dubbo-->
           <dependency>
               <groupId>org.apache.dubbo</groupId>
               <artifactId>dubbo</artifactId>
               <version>2.7.8</version>
           </dependency>
          <!-- Zookeeper-->
           <dependency>
               <groupId>org.apache.zookeeper</groupId>
               <artifactId>zookeeper</artifactId>
               <version>3.4.10</version>
           </dependency>      
           <dependency>
               <groupId>org.apache.curator</groupId>
               <artifactId>curator-framework</artifactId>
               <version>2.12.0</version>
           </dependency>
           <dependency>
               <groupId>org.apache.curator</groupId>
               <artifactId>curator-recipes</artifactId>
               <version>2.12.0</version>
           </dependency>
       </dependencies>
   
   </project>
   ```

   

## 定义服务接口 

```java
public interface UserService {
    User queryUserById(int id);
}

public interface OrderService {
    String sendOrder(int id);
}
```



## 服务提供者

设置两个服务提供者，UserService 和 OrderService，其中 OrderService 不仅会提供自身的服务，也会消费 UserService 的服务，它依赖于 UserService。

### 服务提供方实现接口

UserService 服务的实现

```java
public class UserServiceImpl implements UserService {

    @Override
    public User queryUserById(int id) {
        User user = null;
        if (id == 1) user = new User(1, "老八", "撤硕");
        else if (id == 2) user = new User(2, "潘子", "长江");
        else if (id == 3) user = new User(3, "嘎子", "白洋淀");
        else user = new User(4, "马老师", "嘤国");
        return user;
    }
}
```



OrderService 服务的实现，该实现会远程调用 UserService 服务的实现

```java
public class OrderServiceImpl implements OrderService {
		// 这里需要调用 userService 的远程实现
    UserService userService;

    public void setUserService(UserService userService) {
        this.userService = userService;
    }

    @Override
    public String sendOrder(int id) {
        User user = userService.queryUserById(id);
        String msg = "Hello " + user.getName() + ", this is your order.";
        return msg;
    }
}
```





### 配置声明暴露服务

UserService 服务配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:dubbo="http://dubbo.apache.org/schema/dubbo"
       xmlns="http://www.springframework.org/schema/beans"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans-4.3.xsd
       http://dubbo.apache.org/schema/dubbo
       http://dubbo.apache.org/schema/dubbo/dubbo.xsd">

    <!-- 提供方应用信息，用于计算依赖关系 -->
    <dubbo:application name="user-provider-app" id="user-provider-app"/>

    <!-- 使用 zookeeper 广播注册中心暴露服务地址 -->
    <dubbo:registry protocol="zookeeper" address="120.79.198.81:2181,120.79.198.81:2182,120.79.198.81:2183"/>

    <!-- 指定通信规则和通信端口 用 dubbo 协议在 20880 端口暴露服务 -->
    <dubbo:protocol name="dubbo" port="20880"/>

    <!-- 将 UserService 的本地实现注入容器 -->
    <bean id="userServiceImpl" class="userprovider.service.impl.UserServiceImpl"/>

    <!-- 声明需要暴露的服务接口，引用本地实现  -->
    <dubbo:service interface="interf.service.UserService" ref="userServiceImpl"/>

</beans>
```



OrderService 服务配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:dubbo="http://dubbo.apache.org/schema/dubbo"
       xmlns="http://www.springframework.org/schema/beans"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans-4.3.xsd
       http://dubbo.apache.org/schema/dubbo
       http://dubbo.apache.org/schema/dubbo/dubbo.xsd">

    <!-- 提供方应用信息，用于计算依赖关系 -->
    <dubbo:application name="order-provider-app"/>

    <!-- 使用 zookeeper 注册中心暴露服务地址 -->
    <dubbo:registry protocol="zookeeper" address="120.79.198.81:2181,120.79.198.81:2182,120.79.198.81:2183"/>

    <!-- 指定通信规则和通信端口 用 dubbo 协议在 20881 端口暴露服务 -->
    <dubbo:protocol name="dubbo" port="20881"/>
    
    <!-- 生成远程服务代理，将远程服务的实现注入本地容器，可以和本地 bean 一样使用 -->
    <!-- 远程服务组件的 id 即为标签中的 id 值 -->
    <dubbo:reference id="userServiceImpl" interface="interf.service.UserService"/>

    <!-- 将 UserService 的远程实现（本地 bean）注入 OrderService 的本地实现 -->
    <bean id="orderServiceImpl" class="orderprovider.service.impl.OrderServiceImpl">
        <!--suppress InjectionValueTypeInspection -->
        <property name="userService" ref="userServiceImpl"/>
    </bean>

    <!-- 声明需要暴露的服务接口 -->
    <dubbo:service interface="interf.service.OrderService" ref="orderServiceImpl"/>

</beans>
```



### 加载配置提供远程服务

```java
public class UserProviderApplication {

    public static void main(String[] args) throws IOException {
        ApplicationContext ac = new ClassPathXmlApplicationContext("spring-user.xml");
        System.in.read();
    }
}

public class OrderProviderApplication {

    public static void main(String[] args) throws IOException {
        ApplicationContext ac = new ClassPathXmlApplicationContext("spring-order.xml");
        System.in.read();
    }
}
```



## 服务消费者

设置一个消费者，消费 OrderService 提供的服务

### 配置引用远程服务

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:dubbo="http://dubbo.apache.org/schema/dubbo"
       xmlns="http://www.springframework.org/schema/beans"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans-4.3.xsd
       http://dubbo.apache.org/schema/dubbo
       http://dubbo.apache.org/schema/dubbo/dubbo.xsd">

    <!-- 消费方应用名，用于计算依赖关系，不是匹配条件，不要与提供方一样 -->
    <dubbo:application name="consumer-app"/>

    <!-- 使用 zookeeper 广播注册中心暴露服务地址 -->
    <dubbo:registry protocol="zookeeper" address="120.79.198.81:2181,120.79.198.81:2182,120.79.198.81:2183"/>

    <!-- 生成远程服务代理，将 OrderService 的远程实现注入本地 ioc 容器 -->
    <dubbo:reference id="orderServiceImpl" interface="interf.service.OrderService"/>

</beans>
```



### 加载配置调用远程服务

```java
public class ConsumerApplication {
    public static void main(String[] args) throws IOException {
        ApplicationContext ac = new ClassPathXmlApplicationContext("spring.xml");
        OrderService orderService = (OrderService) ac.getBean("orderServiceImpl");
        String s = orderService.sendOrder(1);
        System.in.read(); // 按任意键退出
    }
}
```







# Dubbo-Springboot🌀

## 项目构建

Springboot 整合 Dubbo 主要有三种方式：

1. 在 application.propertirs 中写入 Dubbo 相关配置
2. 使用 `@ImportResource` 引入 Dubbo 的 xml 配置
3. 使用配置文件类的 API 设置 ，再手动将配置文件注入容器

以下只演示最为常用的第一种方式



### 项目架构

```ruby
spring-dubbo
     |-------- interface-api
     |-------- dubbo-provider-user
     |-------- dubbo-provider-order
     |-------- dubbo-consumer
```



### 依赖

1. 父项目

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <project xmlns="http://maven.apache.org/POM/4.0.0"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
       <modelVersion>4.0.0</modelVersion>
   
       <groupId>kzq.yorua</groupId>
       <artifactId>dubbo-springboot-learn</artifactId>
       <packaging>pom</packaging>
       <version>1.0-SNAPSHOT</version>
       <parent>
           <artifactId>spring-boot-starter-parent</artifactId>
           <groupId>org.springframework.boot</groupId>
           <version>2.4.5</version>
       </parent>
       <modules>
           <module>interface-api</module>
           <module>dubbo-provider-user</module>
           <module>dubbo-provider-order</module>
           <module>dubbo-consumer</module>
       </modules>
       <dependencies>
           <!-- Springboot-->
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter</artifactId>
               <version>2.4.5</version>
           </dependency>
       </dependencies>
   </project>
   ```

   

2. 服务提供者

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <project xmlns="http://maven.apache.org/POM/4.0.0"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
       <parent>
           <artifactId>dubbo-springboot-learn</artifactId>
           <groupId>kzq.yorua</groupId>
           <version>1.0-SNAPSHOT</version>
       </parent>
       <modelVersion>4.0.0</modelVersion>
   
       <artifactId>dubbo-provider-order</artifactId>
       <dependencies>
           <!-- API 接口-->
           <dependency>
               <groupId>kzq.yorua</groupId>
               <artifactId>interface-api</artifactId>
               <version>1.0-SNAPSHOT</version>
           </dependency>
           <!-- Dubbo-->
           <dependency>
               <groupId>org.apache.dubbo</groupId>
               <artifactId>dubbo-spring-boot-starter</artifactId>
               <version>2.7.8</version>
           </dependency>
           <dependency>
               <groupId>org.apache.curator</groupId>
               <artifactId>curator-framework</artifactId>
               <version>2.12.0</version>
           </dependency>
           <dependency>
               <groupId>org.apache.curator</groupId>
               <artifactId>curator-recipes</artifactId>
               <version>2.12.0</version>
           </dependency>
       </dependencies>
   
   </project>
   ```

   

3. 服务消费者

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <project xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xmlns="http://maven.apache.org/POM/4.0.0"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
       <parent>
           <artifactId>dubbo-springboot-learn</artifactId>
           <groupId>kzq.yorua</groupId>
           <version>1.0-SNAPSHOT</version>
       </parent>
       <modelVersion>4.0.0</modelVersion>
   
       <artifactId>dubbo-consumer</artifactId>
       <dependencies>
           <!-- API 接口-->
           <dependency>
               <groupId>kzq.yorua</groupId>
               <artifactId>interface-api</artifactId>
               <version>1.0-SNAPSHOT</version>
           </dependency>
           <!-- Web-->
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-web</artifactId>
           </dependency>
           <!-- Dubbo-->
           <dependency>
               <groupId>org.apache.dubbo</groupId>
               <artifactId>dubbo-spring-boot-starter</artifactId>
               <version>2.7.8</version>
           </dependency>
           <dependency>
               <groupId>org.apache.curator</groupId>
               <artifactId>curator-framework</artifactId>
               <version>2.12.0</version>
           </dependency>
           <dependency>
               <groupId>org.apache.curator</groupId>
               <artifactId>curator-recipes</artifactId>
               <version>2.12.0</version>
           </dependency>
       </dependencies>
   
   </project>
   ```

   

## 定义服务接口

该模块用于提供服务接口

```java
public interface UserService {
    User queryUserById(int id);
}

public interface OrderService {
    String sendOrder(int id);
}
```



## 服务提供者

设置两个服务提供者，UserService 和 OrderService，其中 OrderService 不仅会提供自身的服务，也会消费 UserService 的服务，它依赖于 UserService。

### 服务提供方实现接口

UserService 服务的实现

```java
@DubboService(interfaceClass = UserService.class)
public class UserServiceImpl implements UserService {

    @Override
    public User queryUserById(int id) {
        User user = null;
        if (id == 1) user = new User(1, "老八", "撤硕");
        else if (id == 2) user = new User(2, "潘子", "长江");
        else if (id == 3) user = new User(3, "嘎子", "白洋淀");
        else user = new User(4, "马老师", "嘤国");
        return user;
    }
}
```



OrderService 服务的实现，该实现会远程调用 UserService 服务的实现

```java
@DubboService(interfaceClass = OrderService.class)
public class OrderServiceImpl implements OrderService {
    @DubboReference
    UserService userService;

    @Override
    public String sendOrder(int id) {
        User user = userService.queryUserById(id);
        String msg = "Hello " + user.getName() + ", this is your order.";
        return msg;
    }
}
```



### 配置声明暴露服务

UserService 服务配置

```properties
spring.application.name=dubbo-provider-user
dubbo.application.name=dubbo-provider-user
dubbo.application.id=dubbo-provider-user
dubbo.registry.protocol=zookeeper
dubbo.registry.address=zookeeper://120.79.198.81:2181;zookeeper://120.79.198.81:2182;zookeeper://120.79.198.81:2183
dubbo.protocol.name=dubbo
dubbo.protocol.port=20880
```



OrderService 服务配置

```properties
spring.application.name=dubbo-provider-order
dubbo.application.name=dubbo-provider-order
dubbo.application.id=dubbo-provider-order
dubbo.registry.protocol=zookeeper
dubbo.registry.address=zookeeper://120.79.198.81:2181;zookeeper://120.79.198.81:2182;zookeeper://120.79.198.81:2183
dubbo.protocol.name=dubbo
dubbo.protocol.port=20881
```



### 启动项目

分别启动 dubbo-provider-user 和 dubbo-provider-order 的服务

```java
@EnableDubbo
@SpringBootApplication
public class UserServiceProviderApplication {
    public static void main(String[] args) {
        SpringApplication.run(UserServiceProviderApplication.class, args);
    }
}

@EnableDubbo
@SpringBootApplication
public class OrderServiceProviderApplication {
    public static void main(String[] args) {
        SpringApplication.run(OrderServiceProviderApplication.class, args);
    }
}
```





## 服务消费者

### 配置引用远程服务

```properties
spring.application.name=dubbo-demo-consumer
dubbo.application.name=dubbo-demo-consumer
dubbo.application.id=dubbo-demo-consumer
dubbo.registry.address=zookeeper://120.79.198.81:2181;zookeeper://120.79.198.81:2182;zookeeper://120.79.198.81:2183
```



### 调用远程服务

```java
@RestController
public class OrderController {
    @DubboReference
    OrderService orderService;

    @RequestMapping("/order")
    public String showOrder(@RequestParam("id") int id) {
        return orderService.sendOrder(id);

    }
}
```



### 启动项目

```java
@SpringBootApplication
public class ConsumerApplication {
    public static void main(String[] args) {
        SpringApplication.run(ConsumerApplication.class, args);
    }
}
```





