# Mybatis🍪

Mybatis 是一个优秀的基于 java 的持久层框架，它内部封装了 jdbc，使开发者只需要关注 sql 语句本身，而不需要花费精力去处理加载驱动、创建连接、创建 statement 等繁杂的过程。

Mybatis 通过 xml 或 注解 的方式将要执行的各种 statement 配置起来，并通过 java 对象和 statement 中 sql 的动态参数进行映射生成最终执行的 sql 语句，最后由 mybatis 框架执行 sql 并将结果映射为 java 对象并返回。

Mybatis采用 ORM 思想解决了实体和数据库映射的问题，对 jdbc 进行了封装，不必使用 jdbc 的 API 就可以完成对数据库的持久化操作。



## 参考文档

👉  [Mybatis 官方中文文档](https://mybatis.org/mybatis-3/zh/index.html)



## ORM

ORM： Object Relational Mappging 对象关系映射

指将数据库表和实体类及实体类的属性对应起来，让我们可以操作实体类就实现操作数据库表。



## DAO

数据库访问对象（Database Access Object ，DAO）是一个面向对象的数据库接口，位于业务逻辑和持久化之间，实现对持久化数据的访问，通俗的讲，就是将数据操作封装起来，对外提供相应的接口。





# QuickStart🍪

## 环境搭建

1. 在mysql中创建数据库和表

   ```mysql
   create database mybatis-learn charset=utf8;
   ```

   ```mysql
   CREATE TABLE `user`
   (
       `id`       int(11)     NOT NULL auto_increment,
       `username` varchar(32) NOT NULL COMMENT '用户名称',
       `birthday` datetime     default NULL COMMENT '生日',
       `sex`      char(1)      default NULL COMMENT '性别',
       `address`  varchar(256) default NULL COMMENT '地址',
       PRIMARY KEY (`id`)
   ) ENGINE = InnoDB
     DEFAULT CHARSET = utf8;
   ```

   ```sql
   insert into `user`(`id`, `username`, `birthday`, `sex`, `address`)
   values (41, '老王', '1967-02-27 17:47:08', '男', '北京'),
          (42, '老八', '1983-03-02 15:09:37', '女', '黑龙江'),
          (43, '马保国', '1951-03-04 11:34:34', '女', '山东'),
          (45, '孙笑川', '2000-03-04 12:04:06', '男', '湖北'),
          (46, '林克', '1997-03-07 17:37:26', '男', '北京'),
          (48, '小马宝莉', '1995-03-08 11:44:00', '女', '美国');
   ```

   

2. 新建Maven工程，引入依赖项

   ```xml
   <dependencies>
       <dependency>
           <groupId>org.mybatis</groupId>
           <artifactId>mybatis</artifactId>
           <version>3.4.5</version>
       </dependency>
       <dependency>
           <groupId>mysql</groupId>
           <artifactId>mysql-connector-java</artifactId>
           <version>5.1.6</version>
       </dependency>
       <dependency>
           <groupId>log4j</groupId>
           <artifactId>log4j</artifactId>
           <version>1.2.17</version>
       </dependency>
       <dependency>
           <groupId>junit</groupId>
           <artifactId>junit</artifactId>
           <version>4.10</version>
           <scope>test</scope>
       </dependency>
   
   </dependencies>
   ```

3. 创建实体类和Mapper接口

   ```java
   public interface UserMapper {
       List<User> findAll();
   }
   ```
   ```java
   public class User implements Serializable {
       private Integer id;
       private String username;
       private Date birthday;
       private String sex;
       private String address;
   		
       // 省略 set、get、toString 方法
   }
   ```
   
   


3. 在Resources目录下新建 `SqlMapConfig.xml` 

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE configuration
           PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-config.dtd">
   <!-- Mybatis的主配置文件 -->
   <configuration>
       <!-- 配置环境 -->
       <environments default="mysql">
           <!-- 配置mysql 的环境-->
           <environment id="mysql">
               <!-- 配置事务类型 -->
               <transactionManager type="JDBC"></transactionManager>
               <!-- 配置连接池 -->
               <dataSource type="POOLED">
                   <property name="driver" value="com.mysql.jdbc.Driver"/>
                   <property name="url" value="jdbc:mysql://localhost:3306/mybatis-learn"/>
                   <property name="username" value="root"/>
                   <property name="password" value="kzqkzq"/>
               </dataSource>
           </environment>
       </environments>
       <!-- 指定映射配置文件的位置，映射配置文件指的是每个 dao 独立的配置文件 -->
       <!-- resources路径下的xml文件，注意文件夹结构要用'/'分隔 -->
       <mappers>
           <mapper resource="kzq/yorua/dao/IUserDAO.xml"></mapper>
       </mappers>
   </configuration>
   ```
   
   



## xml配置

1. 在 resources 中 kzq/yorua/mapper 路径下新建映射配置文件 `UserMapper.xml`

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE mapper
           PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <mapper namespace="kzq.yorua.mapper.UserMapper">
       <select id="findAll" resultType="kzq.yorua.domain.User">
           select * from user;
       </select>
   </mapper>
   ```




2. 目录结构如下

```ruby
├── log
│   └── axis.log
├── pom.xml
├── src
│   ├── main
│   │   ├── java
│   │   │   └── kzq
│   │   │       └── yorua
│   │   │           ├── mapper
│   │   │           │   └── UserMapper.java
│   │   │           └── domain
│   │   │               └── User.java
│   │   └── resources
│   │       ├── SqlMapConfig.xml
│   │       ├── kzq
│   │       │   └── yorua
│   │       │       └── mapper
│   │       │           └── UserMapper.xml
│   │       └── log4j.properties
│   └── test
│       └── java
│           └── kzq
│               └── yorua
│                   └── MybatisTest.java
└── target
```





## 测试类

```java
public class MybatisTest {
    public static void main(String[] args) throws Exception {
        // 1. 读取配置文件
        InputStream is = Resources.getResourceAsStream("SqlMapConfig.xml");
        // 2. 创建SqlSessionFactory
        SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
        SqlSessionFactory factory = builder.build(is);
        // 3. 使用工厂生产SqlSession对象，如果这里传true，表示自动提交事务
        SqlSession session = factory.openSession();
        // 4. 使用SqlSession对象创建Dao接口的代理对象
        IUserDAO userDAO = session.getMapper(IUserDAO.class);
        // 5. 使用代理对象执行方法
        List<User> list = userDAO.findAll();

        for (User user : list) {
            System.out.println(user);
        }
        // 6. 释放资源
        session.close();
        is.close();
    }
}
```



## 测试类重构



```java
public class MybatisTest {
    private InputStream is;
    private SqlSession sqlSession;
    private IUserMapper userMapper;

    @Before
    public void init() throws Exception {
        is = Resources.getResourceAsStream("SqlMapConfig.xml");
        SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
        SqlSessionFactory factory = builder.build(is);
        sqlSession = factory.openSession();
        // 使用SqlSession对象创建IuserMapper接口的代理对象
        userMapper = sqlSession.getMapper(IUserMapper.class);
    }

    @After
    public void destory() throws Exception {
        sqlSession.close();
        is.close();
    }

    @Test
    public void testSave() {
        List<User> list = userMapper.findAll();
        for (User user : list) {
            System.out.println(user);
        }
    }
}
```



## 注意事项

1. Mybatis 的映射配置文件位置必须和 mapper 接口的包结构相同

2. 映射配置文件中，mapper 标签的 namespace 属性的取值必须是 mapper 接口的全限定类名
3. 映射配置文件的操作配置（select等）, id 属性的取值必须是 mapper 接口的方法名









# XML配置🍪

👉 [官方文档](https://mybatis.org/mybatis-3/zh/configuration.html#)

Mybatis 的所有配置都必须写在 `<configuration>` 标签中



## properties

作用：用来加载属性文件

properties标签可以直接配置属性，也可以引入外部配置文件



### 直接配置属性

```xml
<properties>
		<property name="jdbc.driver" value="com.mysql.jdbc.Driver"/> 
		<property name="jdbc.url" value="jdbc:mysql://localhost:3306/mybatis-learn"/>
  	<property name="jdbc.username" value="root"/>
		<property name="jdbc.password" value="kzqzkq"/> 
</properties>
```



### 引入外部属性

```properties
# jdbcConfig.properties  外部配置文件必须位于类路径下
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/mybatis-learn
jdbc.username=root
jdbc.password=kzqkzq
```



1. resource引入

   ```xml
   <properties resource="jdbcConfig.properties"></properties>
   ```



2. url引入

   url 参数的格式是 File Protocol（本地文件传输协议）

   ```xml
   <properties url="file:///Users/yorua/Mybatis/src/main/resources/jdbcConfig.properties">
   </properties>
   ```

   



### 引用配置

```xml
<dataSource type="POOLED">
    <property name="driver" value="${jdbc.driver}"/>
    <property name="url" value="${jdbc.url}"/>
    <property name="username" value="${jdbc.username}"/>
    <property name="password" value="${jdbc.password}"/>
</dataSource>
```



## settings

全局参数配置，决定 MyBatis 的运行时行为。







## typeAliases

类型别名可为 Java 类型设置一个缩写名字。 它仅用于 XML 配置，意在降低冗余的全限定类名书写

### 指定实体类

- type：指定的是实体类全限定类名

- alias：指定别名，指定别名之后，不再区分大小写

```xml
<typeAliases>
    <typeAlias type="kzq.yorua.domain.User" alias="user"></typeAlias>
</typeAliases>
```



### 指定包

- package：用于指定要配置別名的包，指定之后该包下的实体类都会注册別名，并且类名就是别名，不再区分大小写

- name：包路径

```xml
<typeAliases>
    <package name="kzq.yorua.domain"/>
</typeAliases>
```





## environments

MyBatis 可以配置成适应多种环境，这种机制有助于将 SQL 映射应用于多种数据库之中

尽管可以配置多个环境，但每个 `SqlSessionFactory` 实例只能选择一种环境。

为了指定创建哪种环境，只要将它作为可选的参数传递给 `SqlSessionFactoryBuilder`

```java
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader, environment);
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader, environment, properties);
```

如果忽略了环境参数，那么将会加载默认环境。



`<environments>` 元素定义了如何配置环境。

```xml
<environments default="development">
  <environment id="development">
    <transactionManager type="JDBC">
      <property name="..." value="..."/>
    </transactionManager>
    <dataSource type="POOLED">
      <property name="driver" value="${driver}"/>
      <property name="url" value="${url}"/>
      <property name="username" value="${username}"/>
      <property name="password" value="${password}"/>
    </dataSource>
  </environment>
</environments>
```



### transactionManager



### dataSource

Mybatis 将数据源分为三类：

- UNPOOLED：不使用连接池的数据源  
- POOLED：使用连接池的数据源 ，采用传统的 javax.sql.Datasource 规范中的连接池
- JNDI：使用 JNDI 实现的数据源

MyBatis 在初始化时，根据 `<dataSource>` 的 type 属性来创建相应类型的的数据源 DataSource： 

- `type="POOLED"`，MyBatis 会创建 PooledDataSource 实例

- `type="UNPOOLED"`，MyBatis 会创建 UnpooledDataSource 实例

- `type="JNDI"`，MyBatis 会从 JNDI 服务上查找 DataSource 实例，然后返回使用



## mappers

`<mappers>` 用于告知 Mybatis 映射文件的位置



### 通过映射文件加载

mapper标签可以通过resource方法加载单个映射文件的

```xml
<mappers>
    <mapper resource="kzq/yorua/mapper/UserMapper.xml"></mapper>
</mappers>
```



### 通过映射接口加载

mapper 接口中应使用注释的方式写sql

这种方式需要将mapper接口类名和mapper.xml映射文件名称保持一致，且拥有相同的目录结构

```xml
<mappers>
    <mapper class="kzq.yorua.mapper.UserMapper.class"></mapper>
</mappers>
```



### 批量加载

可以加载指定包名下的所有 mapper 接口

这种方式需要将 mapper 接口类名和 mapper.xml 映射文件名称保持一致，且拥有相同的目录结构

```xml
<mappers>
    <package name="kzq.yorua.mapper" />
</mappers>
```





# XML映射文件🍪

在写Mybatis相关参数时，基本类型和String可以直接写类型名称，也可以使用 `包名.类名` 的方式，实体类类型，只能使用全限定类名。

Mybaits在加载时已经把常用的数据类型注册了别名，从而在使用时可以不写包名，而实体类并没有注册别名，所以必须写全限定类名，可以使用typeAliases标签为实体类注册别名。



## select



## insert, update, delete



## parameterType

```xml
<delete id="deleteUser" parameterType="java.lang.Integer">
    delete from user where id = #{id};
</delete>
```

```xml
<select id="findByName" parameterType="String" resultType="kzq.yorua.domain.User">
    select * from user where username like #{name}
</select>
```



## resultType

resultType将查询到结果映射封装成 pojo 类型，前提是该 pojo 类的属性名和查询到的数据库表的字段名一致



### 返回一般数据类型

```xml
<select id="getUsernameById" resultType="string">
    select username from user where id = #{id}
</select>
```



### 返回javabean

```xml
<select id="findAll" resultType="kzq.yorua.domain.User">
    select * from user;
</select>
```



### 返回Map

此时resultType的值固定为map

```xml
<select id="findByIDToMap" resultType="map">
    select * from user where id = #{id};
</select>
```





## resultMap

resultMap标签可以建立查询的列名和实体类的属性名称不一致时建立对应关系。

| 实体类的属性名 | 数据库的列名 |
| -------------- | ------------ |
| userID         | Id           |
| userName       | Username     |
| userAddress    | address      |
| userBirthday   | birthday     |
| userSex        | sex          |



### 定义映射关系

type：指定实体类的全限定类名

id：给定的唯一resultMap标识。

property：主键在pojo中的属性名

column：主键在数据库中的列名

```xml
<resultMap type="kzq.spring.domain.User" id="userMap">
    <id column="id" property="userId"/>
    <result column="username" property="userName"/>
    <result column="sex" property="userSex"/>
    <result column="address" property="userAddress"/>
    <result column="birthday" property="userBirthday"/>
</resultMap>
```



### 配置映射

```xml
<select id="findAll" resultMap="userMap"> 
    select * from user
</select>
```



通过 `${}` 可以将 parameterType 传入的内容拼接在 sql 中且不进行 jdbc 类型转换



## selectKey

mybatis 可以将 insert 数据的主键返回，直接拿到新增数据的主键，以便后续使用

selectKey 会将 `SELECT LAST_INSERT_ID()` 的结果放入到传入的 model 的主键里面

- keyProperty：对应 model 中的主键的属性名
- keyColumn：对应数据库中表的主键列
- resultType：返回类型
- order
  - AFTER：  `SELECT LAST_INSERT_ID()` 在 insert 执行之后执行,多用与自增主键
  - BEFORE： `SELECT LAST_INSERT_ID()` 在 insert 执行之前执行，适合主键非自增的类型

```xml
<insert id="saveUser" parameterType="kzq.yorua.domain.User">
    <selectKey keyProperty="id" keyColumn="id" resultType="int" order="AFTER">
        select last_insert_id()
    </selectKey>
    insert into user(username, birthday, sex, address)
    VALUES (#{username}, #{birthday}, #{sex}, #{address})
</insert>
```



执行一个 insert 操作之后，结果如下：

```
保存操作之前 -> User{id=null, username='小马宝莉', sex='女', address='小马国'}
保存操作之后 -> User{id=4, username='小马宝莉', sex='女', address='小马国'}
```





## #{}和${}

`#{}` 和 `${}`  都是占位符：

- `${}` 表示拼接 sql 语句

  `where username=${username}`，如果传入的值是 111 ，那么解析成 sql 时的值为 `where username=111`

- `#{}` 将传入的数据都当成一个字符串，会对自动传入的数据加一个双引号。**可以很大程度防止 SQL 注入**

  `where username=#{username}`，如果传入的值是 111 ，那么解析成 sql 时的值为 `where username="111"`

总结：一般能用 # 的就别用 $



# 动态SQL🍪

## trim

trim 元素的主要功能是可以在包含的内容前加上某些前缀，也可以在其后加上某些后缀，与之对应的属性是prefix 和 suffix 。

| 属性            | 描述                                                         |
| --------------- | ------------------------------------------------------------ |
| prefix          | 给 sql 语句拼接的前缀                                        |
| suffix          | 给 sql 语句拼接的后缀                                        |
| prefixOverrides | 去除 sql 语句前面的关键字或者字符，该关键字或者字符由 prefixOverrides 属性指定，假设该属性指定为“AND”，当 sql 语句的开头为“AND”，trim 标签将会去除该“AND” |
| suffixOverrides | 去除sql语句后面的关键字或者字符，该关键字或者字符由 suffixOverrides 属性指定 |



##  if 

如果if条件判断的结果全为false，就会导致sql失效，使where没有意义，所以要加一个 `1 = 1`

```xml
<select id="findByCondition01" resultType="User">
    select * from user where 1 = 1
    <if test="username != null">
        and username = #{username}
    </if>
    <if test="address != null">
        and address = #{address}
    </if>
</select>
```

但是，添加了 `where 1 = 1` 的过滤条件之后，数据库系统就无法使用索引等查询优化策略，将会被迫对每行数据进行扫描（即全表扫描） 来比较此行是否满足过滤条件，当表中的数据量较大时查询速度会非常慢；此外，还会存在 SQL 注入的风险。

所以，推荐使用 `<where>` 标签



## where

```xml
<select id="findByCondition02" resultType="User">
    select * from user
    <where>
        <if test="username != null">
            and username = #{username}
        </if>
        <if test="address != null">
            and address = #{address}
        </if>
    </where>
</select>
```



## foreach

`<foreach>` 标签用于遍历集合

- collection：代表要遍历的集合元素，注意编写时不要写#{} 

- open：代表语句的开始部分

- close：代表结束部分

- item：代表遍历集合的每个元素，生成的变量名 

- sperator：代表分隔符

```xml
<select id="findByIds" resultType="user" parameterType="queryvo">
    select * from user
    <where>
        <if test="ids != null and ids.size() > 0">
            <foreach collection="ids" open="and id in (" close=")" item="uid" separator=",">
                #{uid}
            </foreach>
        </if>
    </where>
</select>
```





## include

include 标签引用，可以复用 sql 片段



## sql

```xml
<!-- 设置sql模板 -->
<sql id="defaultsql">
    select * from user
</sql>

<!-- 引用sql模板 -->
<select id="findById">
    <include refid="defaultsql"></include>
    where id = #{id}
</select>
```



## choose

有时候我们并不想应用所有的条件，而只是想从多个选项中选择一个。而使用if标签时，只要test中的表达式为 true，就会执行 if 标签中的条件。MyBatis 提供了 choose 元素。if标签是与(and)的关系，而 choose 是或(or)的关系。



# 多表查询🍪

## 映射关系

- 一对一
- 一对多
- 多对一（一对一）
- 多对多

在 mybatis 中，一对多和多对一属于两种不同的情况，比如一个人可以对应多个账户，但每一个账户都只能属于一个用户，所以多个账户对应一个用户实际上还是一个账户对应一个用户。



```sql
CREATE TABLE IF NOT EXISTS `account`
(
    `ID`    int(11) NOT NULL COMMENT '编号',
    `UID`   int(11) default NULL COMMENT '用户编号',
    `MONEY` double  default NULL COMMENT '金额',
    PRIMARY KEY (`ID`),
    KEY `FK_Reference_8` (`UID`),
    CONSTRAINT `FK_Reference_8` FOREIGN KEY (`UID`) REFERENCES `user` (`id`)
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8;
  
  
insert into `account`(`ID`, `UID`, `MONEY`)
values (1, 46, 1000),
       (2, 47, 1000),
       (3, 48, 2000);
```



## 一对一查询



## 一对多查询

假设一个用户对应多个不同的账户

### 实体类

在实体类中添加一个集合属性，泛型为之前创建的Account类

```java
public class User implements Serializable {
    private Integer id;
    private String username;
    private Date birthday;
    private String sex;
    private String address;
    private List<Account> accounts;
		// ...
}

public class Account implements Serializable {
    private int id;
    private int uid;
    private double money;
    // ...
}
```



### 映射

```sql
<select id="findAllAccounts" resultMap="userAccountMap">
    select t1.*, t2.id aid, t2.uid, t2.money
    from user t1
             left join account t2 on t1.id = t2.uid
</select>
```



collection的属性：

- property：pojo中定义的有关另一个pojo的集合属性

- ofType：与从表有关的pojo

```xml
<resultMap id="userAccountMap" type="User">
    <id property="id" column="id"></id>
    <result property="username" column="username"></result>
    <result property="address" column="address"></result>
    <result property="sex" column="sex"></result>
    <result property="birthday" column="birthday"></result>
    <collection property="accounts" ofType="Account">
        <id property="id" column="aid"></id>
        <result property="uid" column="uid"></result>
        <result property="money" column="money"></result>
    </collection>
</resultMap>
```



### 测试结果

可以观察到，多个账户的数据以列表的形式返回，由于sql是左外连接，所以会有空账户数据

```java
User{id=46, username='马云', birthday=Mon Mar 07 00:00:00 CST 1966, sex='男', address='浙江'}
[Account{id=1, uid=46, money=1000.0}, Account{id=3, uid=46, money=998.0}]
------------
User{id=45, username='马保国', birthday=Sun Mar 04 00:00:00 CST 2018, sex='男', address='山东'}
[Account{id=2, uid=45, money=2000.0}]
------------
User{id=14, username='老八', birthday=Wed Dec 02 00:00:00 CST 2020, sex='男', address='南京'}
[]
------------
```



## 多对多查询

用户和角色是一个多对多的关系

一个用户可以有多个角色，一个角色可以赋予多个用户

对于多对多的关系，需要使用中间表，在中间表中，原表各自的主键，在中间表中是外键。

多对多的查询需要实现以下效果：

- 当我们查询用户时，可以同时得到用户所包含的角色信息

- 当我们查询角色时，可以同时得到角色的所赋予的用户信息



```xml
<select id="findAll" resultMap="userRoleMap">
    select u.*, r.id rid, r.role_name, r.role_desc
    from user u
             left join user_role ur on u.id = ur.uid
             left join role r on ur.rid = r.id
</select>
```



```xml
<resultMap id="userRoleMap" type="kzq.yorua.domain.User">
    <id property="id" column="id"></id>
    <result property="username" column="username"></result>
    <result property="address" column="address"></result>
    <result property="sex" column="sex"></result>
    <result property="birthday" column="birthday"></result>
    <collection property="roles" ofType="kzq.yorua.domain.Role">
        <id property="roleId" column="rid"></id>
        <result property="roleName" column="role_name"></result>
        <result property="roleDescription" column="role_desc"></result>
    </collection>

</resultMap>
```





# Mybatis特性🍪

## 延时加载

### 概念

延迟加载： 需要用到数据时才进行加载，不需要用到数据时就不加载数据，迟加载也称懒加载.

- 优点：先从单表查询，需要时再从关联表去关联查询，大大提高数据库性能，因为查询单表要比关联查询多张表速
  度要快。
- 缺点：只有当需要用到数据时，才会进行数据库查询，这样在大批量数据查询时，因为查询工作也要消耗
  时间，所以可能造成用户等待时间变长，造成用户体验下降。

一对多，多对多：通常情况下采用延退加载。

一对一（多对一）：通常情况下采用立即加载。



### 配置延时加载

在 `SqlMapConfig.xml` 中配置

```xml
<settings>
  	<!-- 允许懒加载 -->
    <setting name="lazyLoadingEnabled" value="true"/>
  	<!-- 每个延迟加载属性会按需加载 -->
    <setting name="aggressiveLazyLoading" value="false"/>
</settings>
```





## 缓存

缓存是存在于内存中的临时数据，其目的是减少和数据库的交互次数，提高执行效率。

什么样的数据能使用缓存？

- 经常查询并且不经常改变的。
- 数据的正确与否最终结果影响不大的



### 一级缓存

一级缓存是 Mybatis 中 Sqlsession 对象的缓存。

当我们执行查询之后，查询的结果会同时缓存到 Sqlsession 为提供的一块区域中，该区域的结构是一个Map。当我们再次查询同样的数据，Mybatis 会先去 Sqlsession 中查询是否有缓存，若存在则直接返回。

当 Sqlsession 对象消失时，mybatis 的一级缓存也就消失了



```java
@Test
public void testFirstCache01() {
    User user1 = userMapper.findById(41);
    User user2 = userMapper.findById(41);
    System.out.println(user1 == user2);  // true
} 
```



```java
@Test
public void testFirstCache02() {
    User user1 = userMapper.findById(41);
    sqlSession.clearCache(); // 清除sqlSession缓存
    User user2 = userMapper.findById(41);
    System.out.println(user1 == user2);  // false
}
```



当调用 Sqlsession 的修改、添加、删除、commit、close等方法时，就会清空一级缓存。

```java
@Test
public void testClearCache() {
    User user1 = userMapper.findById(41);
    User user = new User();
    user.setId(41);
    user.setUsername("汤姆");
    user.setAddress("青岛");
    userMapper.updateUserById(user);
    User user2 = userMapper.findById(41);
    System.out.println(user1 == user2);  // false
}
```



### 二级缓存

二级缓存是 mapper 映射级别的缓存，多个 SqlSession 去操作同一个 Mapper 映射的 sql 语句，多个
SqlSession 可以共用二级缓存，二级缓存是跨 SqlSession 的。



二级缓存的使用步骤：

1. 让 Mybatis 框架支持二级缓存（在 `SqlMapConfig.xml` 中配置）

   ```xml
   <settings>
       <setting name="cacheEnabled" value="true"/>
   </settings>
   ```

   

2. 让当前的映射文件支持二级缓存（在 `UserMapper.xml` 中配置）

   ```xml
   <mapper namespace="kzq.yorua.mapper.IUserMapper">
       <!-- 开启对二级缓存的支持 -->
       <cache></cache>
   </mapper>
   ```
   
   
   
3. 让当前的操作支持二级缓存（在 `select` 标签中配置）

   ```xml
   <select id="findById" resultType="User" parameterType="java.lang.Integer" useCache="true">
       select * from user where id = #{id}
   </select>
   ```

   

二级缓存中保存的是数据，而不是对象，发起新的用户查询时，实际上直接利用缓存中的数据创建了新的用户对象

```java
@Test
public void testCache() {
    SqlSession sqlSession01 = factory.openSession();
    IUserMapper mapper01 = sqlSession01.getMapper(IUserMapper.class);
    User user1 = mapper01.findById(41);

    SqlSession sqlSession02 = factory.openSession();
    IUserMapper mapper02 = sqlSession02.getMapper(IUserMapper.class);
    User user2 = mapper02.findById(41);

    System.out.println(user1 == user2);  // false

}
```



# 注解配置🍪

## 常用注解

```java
@Insert:实现新增
@Update:实现更新
@Delete:实现删除
@Select:实现查询 
@Result:实现结果集封装
@Results:可以与@Result 一起使用，封装多个结果集 
@ResultMap:实现引用@Results 定义的封装
@One:实现一对一结果集封装 
@Many:实现一对多结果集封装 
@SelectProvider: 实现动态SQL映射 
@CacheNamespace:实现注解二级缓存的使用
```



## CRUD

```java
@Select("select * from user")
List<User> findAll();

@Insert("insert into user(username, birthday, sex, address) values (#{username}, #{birthday}, #{sex}, #{address})")
void saveUser(User user);

@Update("update user set username = #{username}, address = #{address} where id = #{id}")
void updateUserById(User user);

@Delete("delete from user where id = #{id}")
void deleteUserById(Integer id);

@Select("select * from user where id = #{id}")
User findById(Integer id);

```





## 复杂关系映射



```java
@Select("select * from user")
@Results(id = "userMap", value = {
        @Result(id = true, column = "id", property = "id"),
        @Result(column = "username", property = "username"),
        @Result(column = "birthday", property = "birthday"),
        @Result(column = "sex", property = "sex"),
        @Result(column = "address", property = "address"),
})
List<User> findAll();

@ResultMap(value = {"userMap"})
@Select("select * from user where id = #{id}")
User findById(Integer id);
```



## 一对一

```java
@Select("select * from account")
@Results(id = "accountMap", value = {
        @Result(id = true, column = "id", property = "id"),
        @Result(column = "uid", property = "uid"),
        @Result(column = "money", property = "money"),
        @Result(column = "uid", property = "user", javaType = User.class,
                one = @One(select = "kzq.yorua.mapper.IUserMapper.findById",
                        fetchType = FetchType.EAGER))
})
List<Account> findAll();
```



```java
@Select("select * from user where id = #{id}")
User findById(Integer id);
```



## 一对多



```java
@Select("select * from user")
@Results(id = "userMap", value = {
        @Result(id = true, column = "id", property = "id"),
        @Result(column = "username", property = "username"),
        @Result(column = "birthday", property = "birthday"),
        @Result(column = "sex", property = "sex"),
        @Result(column = "address", property = "address"),
        @Result(column = "id",
                property = "accounts",
                many = @Many(select = "kzq.yorua.mapper.IAccountMapper.findByUid",
                        fetchType = FetchType.LAZY))
})
List<User> findAll();
```



```java
@Select("select * from account where uid = #{uid}")
List<Account> findByUid(Integer uid);
```





# 日志🍪

## 自动日志发现

Mybatis内置的日志工厂提供日志功能，具体的日志实现有以下几种工具：

- SLF4J
- Apache Commons Logging
- Log4j 2
- Log4j
- JDK logging

具体选择哪个日志实现工具由 MyBatis 的内置日志工厂确定。它会使用最先找到的（按上文列举的顺序查找）。如果一个都未找到，日志功能就会被禁用。



## 设置日志实现

可以在核心配置文件 settings 设置日志实现，如果此设置不存在，则会自动发现日志记录实现。

```xml
<settings>
   <setting name="logImpl" value="LOG4J"/>
</settings>
```



# MyBatis Generator🍪

MyBatis Generator：简称MBG，是一个专门为 MyBatis 框架使用者定制的代码生成器，可以快速的根据表生成对应的映射文件，接口，以及 bean 类。

支持基本的增删改查，以及QBC风格的条件查询。但是表连接、 存储过程等这些复杂 sql 的定义需要我们手工编写。



## Example

在生成后的 pojo类 中，有一部分是名字为 `XxxExample` 的类出现，在这些类中有三个成员变量：

- `distinct`：用于指定 `DISTINCT` 查询。
- `orderByClause`：用于指定 `ORDER BY` 条件，这个条件没有构造方法,直接通过传递字符串值指定。
- `oredCriteria`：用于自定义查询条件。

```java
protected boolean distinct;

protected String orderByClause;

protected List<Criteria> oredCriteria;
```





# Mybatis-Spring🍪

## 参考文档

👉  [Mybatis-Spring 官方中文文档](http://mybatis.org/spring/zh/index.html)



## 流程

在 MyBatis-Spring 中，可使用 `SqlSessionFactoryBean`  来创建 `SqlSessionFactory` 。

```xml
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
  <property name="dataSource" ref="dataSource" />
</bean>
```

将 `SqlSessionFactory` 加入 ioc 容器

```java
@Configuration
public class MyBatisConfig {
  @Bean
  public SqlSessionFactory sqlSessionFactory() throws Exception {
    SqlSessionFactoryBean factoryBean = new SqlSessionFactoryBean();
    // 接收一个 DataSource 对象
    factoryBean.setDataSource(dataSource);
    return factoryBean.getObject();
  }
}
```

定义一个如下的 mapper 接口：

```java
public interface UserMapper {
  @Select("SELECT * FROM users WHERE id = #{userId}")
  User getUser(@Param("userId") String userId);
}
```

那么可以通过 `MapperFactoryBean` 将接口加入到 Spring 中:

```xml
<bean id="userMapper" class="org.mybatis.spring.mapper.MapperFactoryBean">
  <property name="mapperInterface" value="org.mybatis.spring.sample.mapper.UserMapper" />
  <property name="sqlSessionFactory" ref="sqlSessionFactory" />
</bean>
```



配置好之后，可以像 Spring 中普通的 bean 注入方法那样，将映射器注入到业务或服务对象中。`MapperFactoryBean` 将会负责 `SqlSession` 的创建和关闭。 如果使用了 Spring 的事务功能，那么当事务完成时，session 将会被提交或回滚。最终任何异常都会被转换成 Spring 的 `DataAccessException` 异常。



## SqlSessionFactoryBean

在基础的 MyBatis 用法中，是通过 `SqlSessionFactoryBuilder` 来创建 `SqlSessionFactory` 的。而在 MyBatis-Spring 中，则使用 `SqlSessionFactoryBean` 来创建。

 `SqlSessionFactoryBean` 实现了 Spring 的 `FactoryBean` 接口





# Mybatis-Springboot🍪

## 参考文档

👉  [Mybatis-Springboot 官方中文文档](http://mybatis.org/spring-boot-starter/mybatis-spring-boot-autoconfigure/)



## 流程

MyBatis-Spring-Boot-Starter will:

- Autodetect an existing `DataSource`
- Will create and register an instance of a `SqlSessionFactory` passing that `DataSource` as an input using the `SqlSessionFactoryBean`
- Will create and register an instance of a `SqlSessionTemplate` got out of the `SqlSessionFactory`
- Auto-scan your mappers, link them to the `SqlSessionTemplate` and register them to Spring context so they can be injected into your beans



## 配置

```properties
# application.properties

# mapper.xml 文件的位置，如果使用 @Mapper 注解，可以不使用该配置
mybatis.mapper-locations=classpath:mapper/*.xml

# 配置别名
type-aliases-package: kzq.yorua.domain
```



## 相关注解

### @Mapper

标注在 mapper 接口类上，在编译之后会生成相应的接口实现类，并将实现类加入 ioc 容器

```java
@Mapper
public interface UserMapper {
    @Select("select * from user")
    List<User> findAll();
}
```



### @MapperScan

扫描指定包下所有的接口类，然后所有接口在编译之后都会生成相应的实现类并加入 ioc 容器

使用 `@MapperScan` 后，接口类就不需要使用 `@Mapper` 注解。

在 `SpringBootApplication` 启动类上添加，会自动扫描启动类下所有包中的 mapper 接口，也可以指定包名

```

```









# FAQ

## 如何防止 SQL 注入？



