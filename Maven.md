# Maven概念🌀

Maven是一个项目管理工具，它包含了一个项目对象模型（Project Object Model, PM），一组标准集合，一个项目生命周期（Project Lifecycle），一个依赖管理系统（Dependency Management System），和用来运行定义在生命周期阶段中插件目标的逻辑。



## 参考文档

👉 [官方文档](https://maven.apache.org/index.html)

👉  [Maven中央仓库](https://mvnrepository.com/repos/central)

👉  [阿里云云效 Maven](https://maven.aliyun.com/mvn/guide)



## 介绍

传统的 web 项目，jar 包在项目中

Maven 开发的 web 顶目，jar 包不在项目中

Maven 会根据 jar 包的坐标，去 jar 包仓库中寻找资源

Maven 工程的本地仓库默认目录：`${user.home}/.m2/repository`



## 机制

maven工程中不直接将jar包导入到工程中，而是通过在 `pom.xml` 文件中添加所需jar包的坐标，这样就很好的避免了将jar包直接引入进来，在需要用到jar包的时候, 只要查找 `pom.xml` 文件，再通过`pom.xml` 文件中的坐标，到一个专门用于存放jar包的仓库（maven仓库），根椐坐标从而找到jar包。





# Maven标准目录结构🌀

## 文件目录

解压缩 `apache-maven-3.6.3-bin.tar.gz` 后文件目录如下：

- bin：存放了 maven 的命令

- boot：存放了一些 maven 本身的引导程序，如类加载器等 

- conf：存放了 maven 的一些配置文件，如 setting.xml 文件 

- lib：存放了 maven 本身运行所需的一些 jar 包

<img src="http://store.secretcamp.cn/uPic/maven%20.png" style="zoom:67%;float:left" />





## 环境变量

首先解压 maven 文件

```sh
tar -zxvf apache-maven-3.6.3-bin.tar.gz
```



```shell
vim ~/.bashrc

# maven
MAVEN_HOME=/Users/admin/Project/Repository/Maven/Apache-maven-3.6.3
PATH=$PATH:$MAVEN_HOME/bin
```



![image-20201123161618609](http://store.secretcamp.cn/uPic/image-20201123161618609202011231616181606119378ItFyQnItFyQn.png)



## 项目目录结构

`src/main/java`：目录核心代码部分。

`src/main/resources`：配置文件部分

`src/main/webapp`：静态资源

`src/test/java`：目录测试代码部分

`src/test/resources`：测试配置文件。 

`target`：编译生成的文件

`pom.xml`：maven核心配置文件



## 仓库类型

### 本地仓库

用来存储从远程仓库或中央仓库下载的插件和 jar 包，项目使用一些插件或 jar 包，优先从本地仓库査找。

默认本地仓库位置在 `${user.dir}/.m2/repository`



### 中央仓库

在 maven 中内置一个[远程仓库地址]( http://repo1.maven.org/maven2)，它是中央仓库，服务于整个互联网，由 maven 团队自己维护，里面存储了非常全的jar包，它包含了世界上大部分流行的开源项目构件。

中央仓库在 *${MAVEN_HOME}/lib/maven-builder-support-{version}.jar* 中定义

中央仓库关闭了 snapshot 版本构件下载的支持，禁止从公共仓库下载 snapshot 构件是推荐的做法，因为这些构件不稳定，应该避免使用。当然，如果你想使用局域网内组织内部的仓库，可以激活 snapshot 的支持。



### 远程仓库

中央仓库也是远程仓库的一种，一般意义上的远程仓库叫做 "私服"。

如果本地需要插件或者 jar 包，本地仓库没有，默认去远程仓库下载，远程仓库可以在互联网内也可以在局域网内。







# Maven构建流程🌀

项目从编译、测试、运行、打包、安装，部署整个过程都交给 maven 进行管理，这个过程称为「构建」。

构建的每个阶段，都使用一个命令完成

执行后面的命令，总会先执行前面的命令

```
compile -> test -> package -> install -> deploy
```





## 项目构建

### java项目

构建 maven 项目的时候，如果没有进行特殊的配置，Maven会按照标准的目录结构查找和处理各种类型文件。

1. 源代码

   - `src/main/java`
   - `src/test/java` 

   这两个目录中的所有 `*.java` 文件会分别在 comile 和 test-comiple 阶段被编译，编译结果分别放到了 `target/classes` 和 `targe/test-classes` 目录中，但是这两个目录中的其他文件都会被忽略掉。

   

2. 配置文件
   - `src/main/resouces`
   - `src/test/resources`

 这两个目录中的文件也会分别被复制到 `target/classes` 和 `target/test-classes` 目录中。



### web项目

当是web项目时，会在 target 下生成 myproject 目录，myproject是项目名。

1. `src/main/webapps` 目录中的文件会被复制到 `target/myProject` 目录中

2. `target/classes` 目录中的所有内容会被复制到 `target/myProject/WEB-INF/classes` 目录中

3. 默认会将项目的依赖复制到 `target/myProject/WEB-INF/lib`



## -D参数

D 代表 `<properties>` 中的属性

```sh
-Dtheme=halloween clean package # 会将 theme 设置为 halloween

-Dmaven.test.skip=true  # 表示跳过单元测试
```



```xml
<properties>
    <theme>myDefaultTheme</theme>
</properties>
```



## -P参数

P 代表 `<profiles>` 中 profile 的 id

在 `<profiles>` 指定的 `<id>` 中，可以通过 -P 进行传递或者赋值。

```sh
mvn clean package -Ptest  # 将触发 test 环境的 profile 配置
```



```xml
<profiles>
      <profile>
          <id>prod</id>
          ...
      </profile>
      <profile>
          <id>test</id>
          ...
      </profile>
</profiles>
```





## clean

`mvn clean` 是 maven 工程的清理命令，执行 clean 会删除 target 目录及内容。

插件：maven-clean-plugin



## compile

`mvn compile` 是 maven 工程的编译命令，作用是将 src/main/java 下的文件编译为 class 文件输出到 target 目录下。

插件：maven-compile-plugin



## test

`mvn test` 是 maven 工程的测试命令，会执行 src/test/java 下的单元测试类，该命令会同时编译 /src/main 以及 /src/test 下的 java 代码

插件：maven-surefire-pluglin





## package

`mvn package` 是 maven 工程的打包命令，对于 java 工程执行 package 可以打成 jar 包，对于 web 工程执行 package 可以打成 war 包



## install

`mvn install` 是 maven 工程的安装命令，执行 install 将 maven 打成 jar 包或 war 包发布到本地仓库。

插件：maven-install-plugin



## deploy

`mvn deploy` 是 maven 工程的发布命令，用来将项目生成的构件分发到远程 Maven 仓库。

本地 Maven 仓库的构件只能供当前用户使用，在分发到远程 Maven 仓库之后，所有能访问该仓库的用户都能使用此构件。

Maven 区别对待 release 版本的构件和 snapshot 版本的构件，会根据你项目的版本来判断将构件分发到哪个仓库。

snapshot：为开发过程中的版本，实时，但不稳定。

release：比较稳定。





## 生命周期

Maven 对项目构建过程分为三套相互独立的生命周期

Clean Lifecycle ：在进行真正的构建之前进行一些清理工作

Default Lifecycle：构建的核心部分，编译，测试，打包，部署等等。 

Site Lifecycle：生成项目报告，站点，发布站点。





# Maven依赖传递🌀

## 依赖传递示例

有如下传递依赖：A -> B -> C 

![image-20210530160851061](http://store.secretcamp.cn/uPic/image-20210530160851061202105301608511622362131i1uUl6i1uUl6.png)



上述依赖在 pom.xml 中的关系：

```xml
<!-- C (cc-spring-boot-starter-autoconfigure) -->
	<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
  </dependency>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
  </dependency>
 
<!-- B (cc-spring-boot-starter) -->
	<dependency>
    <groupId>cc.lu</groupId>
    <artifactId>cc-spring-boot-starter-autoconfigure</artifactId>
  </dependency>
 
<!-- A (cc-starter-demo) -->
<dependency>
  <groupId>cc.lu</groupId>
  <artifactId>cc-spring-boot-starter</artifactId>
</dependency>
```



## 排除依赖

在项目 A 中，不需要依赖项目 C 中的 `org.springframework.boot:spring-boot-configuration-processor` 包，但是项目 B 仍然需要依赖它，也就是说需要在项目 A 中将这个包排除掉，但是又不能影响其他项目对这个包的依赖。

![image-20210530160729813](http://store.secretcamp.cn/uPic/image-20210530160729813202105301607301622362050x6c9PHx6c9PH.png)



## 可选依赖

可选依赖是指依赖只允许在当前项目使用，从不往下传递，下游项目要是想使用的话需要自己重新引入。

例如 `org.springframework.boot:spring-boot-configuration-processor` 是自动生成配置参数的功能性包，也仅仅就在生成参数声明的时候用到，没必要往下传递，那么就需要在项目 C 控制，严禁它依赖的包向下传递。

项目 A 中，对依赖的 dependency 加上 exclusion 并指定要排除包的 groupId 和 artifactId ，声明 `optional = true` ， 不需要指定版本号，若上有项目依赖了多个相同包的不同版本，则会全部都排除掉。

![image-20210530160335069](http://store.secretcamp.cn/uPic/image-20210530160335069202105301603351622361815dihqxTdihqxT.png)













#  setting.xml🌀

## localRepository

存储构件的本地仓库地址

默认地址是 *${user.home}/.m2/repository* 

```xml
<localRepository>
    /Users/yorua/Project/IdeaProject/repository/Maven/repository
</localRepository>
```



## interactiveMode

Maven 是否需要和用户交互以获得输入。如果 Maven 需要和用户交互以获得输入，则设置成 true，反之则应为 false。默认为true。



## offline

表示 Maven 是否需要在离线模式下运行。如果构建系统需要在离线模式下运行，则为 true，默认为 false 。

当由于网络设置原因或者安全因素，构建服务器不能连接远程仓库的时候，该配置就十分有用。





## pluginGroups

当插件的 groupId 没有显式提供时，供搜寻插件 groupId 的列表

默认情况下该列表包含了 *org.apache.maven.plugins*



## proxies

用来配置不同的代理，多代理 profiles 可以应对笔记本或移动设备的工作环境，通过简单的设置就可以很容易的更换整个代理配置。



## servers

指定连接到特定服务器时要使用的身份验证信息





## mirrors

为仓库列表配置的下载镜像列表

```xml
<mirrors>
    <mirror>
        <!-- 该镜像的唯一标识符，用来区分不同的mirror元素。  --> 
        <id>alimaven</id>
        <!-- 被镜像的服务器的id，这表示中央仓库central的镜像 -->
        <mirrorOf>central</mirrorOf>
        <!-- 镜像名称 --> 
        <name>aliyun maven</name>
        <!-- 该镜像的URL，构建系统会优先考虑使用该URL，而非使用默认的服务器URL -->
        <url>http://maven.aliyun.com/nexus/content/repositories/central/</url>
    </mirror>
</mirrors>
```



## profiles

根据环境参数来调整构建配置的列表，`<profiles>` 由多个 `<profile>` 组成，当所有的约束条件都满足的时候就会激活这个  `<profile>` 

 `<profile>`  含有以下元素：

- id
- activation：自动触发 profile 的条件逻辑
- properties
- repositories
- pluginRepositories

profile 匹配有优先级，首先匹配 jdk，然后才会考虑其他条件

```xml
<profiles>
    <profile>
      	<!-- 该配置的唯一标识符 -->  
        <id>jdk-1.8</id>
        <!-- 自动触发profile的条件逻辑 -->
        <activation>
            <!--profile默认是否激活的标识 --> 
            <activeByDefault>true</activeByDefault>
            <!--activation中内建的java版本检测，如果检测到jdk版本与期待的一样，profile被激活。 --> 
            <!-- 取决于 构建/maven/导入/导入器jdk-->
            <jdk>1.8</jdk>
            <!--如果在pom中检测到某一个属性（通过${名称}引用），与下方有对应的名称和值，profile就会被激活 -->  
            <property>
                <name>jdk-version</name>
                <value>1.8</value>
            </property>     
            <file>  
                <!--如果指定的文件存在，则激活profile。  -->  
                <exists>/path/to/any</exists>  
                <!--如果指定的文件不存在，则激活profile。 -->  
                <missing>/path/to/any/</missing>  
            </file>          
        </activation>
       
        <!-- 如果这个profile被激活，那么以下属性都可以通过 ${} 被访问 --> 
        <properties>
            <maven.compiler.source>1.8</maven.compiler.source>
            <maven.compiler.target>1.8</maven.compiler.target>
            <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
        </properties>
      
        <!--远程仓库列表 -->
        <repositories>
            <!--包含需要连接到远程仓库的信息  -->
            <repository>
                <!--远程仓库唯一标识 -->
                <id>codehausSnapshots</id>
                <!--远程仓库名称  -->
                <name>Codehaus Snapshots</name>
                <!--远程仓库里releases版本的配置 -->
                <releases>
                    <!-- 是否开启releases版的构件 -->
                    <enabled>false</enabled>
                    <!--更新发生的频率。Maven会比较本地POM和远程POM的时间戳-->
                    <!-- always（一直）、daily（默认，每日）、interval：X（分钟）、never（从不）-->
                    <updatePolicy>always</updatePolicy>
                    <!--验证构件校验文件失败时 ignore（忽略）、fail（失败）、warn（警告）-->
                    <checksumPolicy>warn</checksumPolicy>
                </releases>
                <!--远程仓库里snapshots版本的配置 -->
                <snapshots>
                    <enabled>false</enabled>
                    <updatePolicy>always</updatePolicy>
                    <checksumPolicy>warn</checksumPolicy>
                </snapshots>
                <!-- 远程仓库URL -->
                <url>http://snapshots.maven.codehaus.org/maven2</url>
                <!--用于定位和排序构件的仓库布局类型 default（默认）、legacy（遗留）-->
                <layout>default</layout>
            </repository>
        </repositories>  
        <!-- 插件远程仓库列表，配置同上 -->
        <pluginRepositories>
        </pluginRepositories>
    </profile>
</profiles>
```





## activeProfiles

```xml
<activeProfiles>
    <activeProfile>env-test</activeProfile>
</activeProfiles>
```



## 模板

```xml
    <profiles>
        <profile>
            <id>simple-jdk-8</id>
            <activation>
                <activeByDefault>false</activeByDefault>
                <jdk>1.8</jdk>
            </activation>
            <properties>
                <maven.compiler.source>1.8</maven.compiler.source>
                <maven.compiler.target>1.8</maven.compiler.target>
                <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
                <mysql.version>5.1.6</mysql.version>

            </properties>
        </profile>
        <profile>
            <id>simple-jdk-11</id>
            <activation>
                <activeByDefault>true</activeByDefault>
                <jdk>11</jdk>
            </activation>
            <properties>
                <maven.compiler.source>11</maven.compiler.source>
                <maven.compiler.target>11</maven.compiler.target>
                <maven.compiler.compilerVersion>11</maven.compiler.compilerVersion>
                <mysql.version>8.0.23</mysql.version>
            </properties>
        </profile>
    </profiles>
```



# pom.xml🌀

POM 是项目对象模型（Project Object Model）的简称，它是 Maven 项目中的文件。



```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://maven.apache.org/POM/4.0.0"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>kzq.yorua</groupId>
    <artifactId>javase-example</artifactId>
    <version>1.0-SNAPSHOT</version
    <name>JAVA SE EXAMPLE</name>
    <description>Demo project for Spring Boot</description>
</project>
```



## groupId

groupId 是项目组织唯一的标识符，实际对应 java 的包的结构，是 main 目录里 java 的目录结构。



## artifactID

artifactID 就是项目的唯一的标识符，实际对应项目的名称，就是项目根目录的名称。



## name

name 只是一个名称，项目的全名称，可以是大写空格多个词，比如 Spring Boot Starter Parent，而 artifactId 是用来区分同一个 groupId 下的子项目，比如对应的是 spring-boot-starter-parent。

如果不写 `<name>` ，则默认值为 artifactId 的值



## repositories

`<repositories>` 标签的作用是用来配置 maven 项目的远程仓库。



## properties





## profiles

properties 有几种引用方式：

1. `env.X` ：在一个变量前加上 "env." 的前缀，会返回一个 shell 环境变量。

   例如，"env.PATH" 指代了 `$path` 环境变量

2. `project.x`：指代了 POM 中对应的元素值

3. `settings.x`： 指代了settings.xml中对应元素的值

4. Java System Properties：所有可通过 `java.lang.System.getProperties()` 访问的属性都能在 POM 中使用该形式访问

5. 在 `<properties/>` 元素中，以 `${Var}` 的形式使用



## dependencyManagement

在我们项目顶层的 pom 文件中，可以通过 `<dependencyManagement>` 管理 jar 包的版本，让子项目中引用一个依赖而不用显式的列出版本号。

Maven 会沿着父子层次向上走，直到找到一个拥有 `<dependencyManagement>`  元素的项目，然后就会使用在这个 `<dependencyManagement>`  标签中指定的版本号。

 `<dependencyManagement>` 中只是声明依赖，并不实现引入，因此子项目需要显式的声明需要用的依赖。如果不在子项目中声明依赖，是不会从父项目中继承下来的，只有在子项目中写了依赖项，并且没有指定具体版本，才会从父项目中继承该项，如果子项目中指定了版本号，那么会使用子项目中指定的 jar 版本。



## dependencies

在 `pom.xml` 中，存放了项目jar包的依赖信息

`<dependencies>` 标签应在 `<project>` 内

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <scope>runtime</scope>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-configuration-processor</artifactId>
        <optional>true</optional>
    </dependency>
</dependencies>
```



如果本地仓库中没有对应的 jar 包，在 `pom.xml` 中则没有自动补全功能，需要到 Maven 中央仓库中主动寻找需要的 jar 包，再将文件信息依赖项添加到 `<denpendencies></denpendencies>` 中，在联网环境下，就会主动将 jar 包下载到本地仓库



### scope

|  作用域  | 编译有效 | 测试有效 | 运行时有效 |
| :------: | :------: | :------: | :--------: |
| compile  |    ✔️     |    ✔️     |     ✔️      |
|   test   |          |    ✔️     |            |
| provided |    ✔️     |    ✔️     |            |
| runtime  |          |    ✔️     |     ✔️      |
|  system  |    ✔️     |    ✔️     |            |



1. compile

   默认作用域范围就是 compile，即什么都不配置也就是意味着 compile 。

   compile 表示被依赖项目需要参与当前项目的编译，当然后续的测试，运行周期也参与其中，是一个比较强的依赖。打包的时候通常需要包含进去。



2. test

   test 表示依赖项目仅仅参与测试相关的工作，包括测试代码的编译，执行。

   比较典型的如 Junit 。



3. provided

   跟 compile 相似，但是表明了 dependency 由 JDK 或者容器提供。

   例如 Servlet API 和一些 Java EE API。

   这个scope 只能作用在编译和测试时，同时没有传递性。



4. runtime

   表示依赖不作用在编译时，但会作用在运行和测试时。

​	

5. system

   跟 provided 相似，但是在系统中要以外部 jar 包的形式提供，Maven 不会在 repository 查找它。



### optional

- true ：表示依赖不向上传递
- false：默认值，表示依赖向上传递

```xml
<optional>true</optional>
```



### exclusion



## build

`<build>` 标签应在 `<project>` 内，称为 project build，是 `<project>` 的直接子元素



### plugins

`<plugins>` 给出构建过程中所用到的插件

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <excludes>
                    <exclude>
                        <groupId>org.springframework.boot</groupId>
                        <artifactId>spring-boot-configuration-processor</artifactId>
                    </exclude>
                    <exclude>
                        <groupId>org.projectlombok</groupId>
                        <artifactId>lombok</artifactId>
                    </exclude>
                </excludes>
            </configuration>
        </plugin>
    </plugins>
</build>
```



### pluginManagement

作用类似于 `<dependencyManagement>` ，该标签一般出现在父项目中





## 模板

```
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
                <configuration>
                    <source>${maven.compiler.source}</source>
                    <target>${maven.compiler.target}</target>
                </configuration>
            </plugin>
        </plugins>
    </build>
```



# 插件🌀

## maven-assembly-plugin

👉 [官方文档](http://maven.apache.org/plugins/maven-assembly-plugin/)



maven-assembly-plugin 使开发人员能够将项目输出合并到一个单独的可分发存档中，该存档包含依赖项、模块、站点文档和其他文件。



### 配置pom.xml

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-assembly-plugin</artifactId>
            <version>${maven-assembly-plugin.version}<version>
            <executions>
                <execution>
                    <id>make-assembly</id>
                    <!-- 绑定到package生命周期 -->
                    <phase>package</phase>
                    <goals>
                        <!-- 只运行一次 -->
                        <goal>single</goal>
                    </goals>
                </execution>
            </executions>
            <configuration>
                <!-- 配置描述符文件 -->
                <descriptor>assembly.xml</descriptor>
                <!-- 也可以使用Maven预配置的描述符
                <descriptorRefs>
                    <descriptorRef>jar-with-dependencies</descriptorRef>
                </descriptorRefs> -->
            </configuration>
        </plugin>
    </plugins>
</build>

```



### 配置描述符

👉 [自定义描述符](http://maven.apache.org/plugins/maven-assembly-plugin/assembly.html)

👉 [预定义描述符](http://maven.apache.org/plugins/maven-assembly-plugin/descriptor-refs.html)



assembly 插件的打包方式是通过 descriptor（描述符）来定义的，各标签配置在 `assembly.xml` 中。

```xml
<assembly>
    <id>assembly</id>

    <formats>
        <format>tar.gz</format>
    </formats>

    <includeBaseDirectory>true</includeBaseDirectory>
  
		<!-- 设置打包时的属性-->
    <fileSets>
        <fileSet>
            <directory>src/main/bin</directory>
            <includes>
                <include>*.sh</include>
            </includes>
            <outputDirectory>bin</outputDirectory>
            <fileMode>0755</fileMode>
        </fileSet>
        <fileSet>
            <directory>src/main/conf</directory>
            <outputDirectory>conf</outputDirectory>
        </fileSet>
        <fileSet>
            <directory>src/main/sql</directory>
            <includes>
                <include>*.sql</include>
            </includes>
            <outputDirectory>sql</outputDirectory>
        </fileSet>
        <fileSet>
            <directory>target/classes/</directory>
            <includes>
                <include>*.properties</include>
                <include>*.xml</include>
                <include>*.txt</include>
            </includes>
            <outputDirectory>conf</outputDirectory>
        </fileSet>
    </fileSets>

    <files>
        <file>
            <source>target/${project.artifactId}-${project.version}.jar</source>
            <outputDirectory>.</outputDirectory>
        </file>
    </files>

    <dependencySets>
        <dependencySet>
            <unpack>false</unpack>
            <scope>runtime</scope>
            <outputDirectory>lib</outputDirectory>
        </dependencySet>
    </dependencySets>
</assembly>

```



## maven-spring-boot-plugin

👉 [官方文档](https://docs.spring.io/spring-boot/docs/current/maven-plugin/reference/htmlsingle/)







# Idea配置🌀

![image-20201123102756601](http://store.secretcamp.cn/uPic/image-2020112310275660120201123102756160609847647GtwI47GtwI.png)



为了确保在不联网时能够成功创建工程，配置 VM 选项，可以直接从本地寻找之前联网下载过的插件

```
-DarchetypeCatalog=internal
```

![image-20201123102837038](http://store.secretcamp.cn/uPic/image-20201123102837038202011231028371606098517DSeHqADSeHqA.png)



在 src 目录下手动补齐未创建的目录，完整的 maven-webapp 工程目录如图所示：

<img src="http://store.secretcamp.cn/uPic/image-20201123102547951202011231025481606098348lcDqm6lcDqm6.png" alt="image-20201123102547951" style="zoom: 50%;" />



在环境变量中配置 `MAVEN_REPOSITORY` ，这样每次创建 Maven 工程都会自动选择正确的 repository

![image-20201205153248357](http://store.secretcamp.cn/uPic/image-20201205153248357202012051532491607153569xPPOCHxPPOCH.png)







## 存储库

![image-20210616142227872](http://store.secretcamp.cn/uPic/image-20210616142227872XaW0fx1623824548306.png)

