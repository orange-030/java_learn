# Jdbc🍊

Jdbc：Java DataBase Connectivity，即用 Java语言操作数据库

Jdbc 本质：官方（sun公司）定义的一套操作所有关系型数据库的规则，即接口，各个数据库厂商去实现这套接口，提供数据库驱动 jar 包。

Jdbc 只适用于关系型数据库



## DriverManager

注册驱动：告诉程序该使用哪一个数据库驱动 jar

- `static void registerDriver(Driver driver)` ：注册与给定的驱动程序 DriverManager

  例如导入了 mysql 的驱动 jar 包 `mysql-connector-java` ，就可以注册：

  ```java
  Class.forName("com.mysql.jdbc.Driver");
  ```

  

获取数据库连接：

- `static Connection getConnection(String url, String user, String password)` 

  ```markdown
  - url格式
  jdbc:mysql://ip地址(域名):端口号/数据库名称
  jdbc:mysql://localhost:3306/school
  
  - 避免中文乱码：设置characterEncodings
  url=jdbc:mysql://127.0.0.1:3306/school?characterEncoding=utf-8
  ```

  

## Connection

Connection：数据库连接对象

获取执行 sql 的对象：

- `Statement createStatement()`

- `PreparedStatement prepareStatement(String sql)`  

管理事务：

- `setAutoCommit(boolean autoCommit)` ：调用该方法设置参数为false，即开启事务

- `commit()` ：提交事务

- `rollback()` ：回滚事务

```java
public class demo {
    public static void main(String[] args) {
        // 注册驱动
        try {
            Class.forName("com.mysql.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
        Statement statement = null;
        Connection conn = null;
        try {
            // 定义sql
            String sql = "update student set score = 60 where id = 1 ";
            // 获取数据库连接对象
            conn = DriverManager
                .getConnection("jdbc:mysql://localhost:3306/school", "root", "123456");
            // 获取sql执行对象statement
            statement = conn.createStatement();
            // 执行sql
            int count = statement.executeUpdate(sql);
            System.out.println(count);
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        } finally {
            try {
                statement.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
            try {
                conn.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
    }
}
```



## Statement

Statement：执行sql的对象

执行sql：

- `boolean execute(String sql)` ：可以执行任意的sql 

- `int executeUpdate(String sql)` ：执行DML（insert、update、delete）语句、DDL(create，alter、drop)语句

- `ResultSet executeQuery(String sql)`  ：执行DQL（select）语句



```java
public class demo_query {
    public static void main(String[] args) {
        // 注册驱动
        try {
            Class.forName("com.mysql.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
        Statement statement = null;
        Connection conn = null;
        try {
            // 定义sql
            String sql = "select * from student";
            
            // 获取数据库连接对象
            conn = DriverManager.getConnection
                    ("jdbc:mysql://localhost:3306/school", "root", "123456");
            
            // 获取sql执行对象statement
            statement = conn.createStatement();
            
            // 执行sql
            ResultSet rs = statement.executeQuery(sql);
			
            // 遍历结果集 ResultSet
            while (rs.next()) {
                int id = rs.getInt(1);
                String name = rs.getString("name");
                int score = rs.getInt("score");
                int sex = rs.getInt("sex");
                System.out.println(id + "-" + name + "-" + score + "-" + sex);
            }
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        } finally {
            try {
                statement.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
            try {
                conn.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
    }
}
```



## ResultSet

**ResultSet：**结果集对象，封装查询结果

- `boolean next()`：游标向下移动一行，判断当前行是否是最后一行末尾，如果是，则返回false，如果不是则返回true

- `getXXX(参数)`：获取数据

XXX代表数据类型，参数可以是列名（string），也可以是从1开始的索引（int）

```java
while(rs.next()){
    int id = rs.getInt(1);
    String name = rs.getString("name");
    double balance = rs.getDouble(3);
}
```



## PreparedStatement

**PreparedStatement：**执行sql的对象

解决sql注入问题：使用PreparedStatement对象来解决

```
public class demo_06 {
    public static void main(String[] args) {
        PreparedStatement ps1 = null;
        PreparedStatement ps2 = null;
        Connection conn = null;
        try {
            conn = demo_Jdbc_utils.getConnection();
            String sql1 = "update student set score = score + ? where id = ?";
            String sql2 = "update student set score = score - ? where id = ?";
            ps1 = conn.prepareStatement(sql1);
            ps2 = conn.prepareStatement(sql2);
            // 设置参数
            ps1.setInt(1, 20);
            ps1.setInt(2, 1);
            ps2.setInt(1, 20);
            ps2.setInt(2, 2);

            ps1.executeUpdate();
            ps2.executeUpdate();
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        } finally {
            demo_Jdbc_utils.close(null, ps1, conn);
            demo_Jdbc_utils.close(null, ps2, null);
        }
    }
}
```



## JdbcUtils

自己创建一个 JdbcUtils 类，目的是简化数据库连接操作的书写

```JAVA
public class JdbcUtils {
    private static String url;
    private static String user;
    private static String password;
    private static String driver;

	// 静态代码块，类加载时自动执行一次，加载数据库配置
    static {
        try {
            Properties properties = new Properties();
            ClassLoader loader = demo_Jdbc_utils.class.getClassLoader();
            URL resource = loader.getResource("db.properties");
            String path = resource.getPath();
            properties.load(new FileReader(path));

            url = properties.getProperty("url");
            user = properties.getProperty("user");
            password = properties.getProperty("password");
            driver = properties.getProperty("driver");
            Class.forName(driver);
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
        }

    }
    
    
	// 获取连接
    public static Connection getConnection() throws SQLException {

        return DriverManager.getConnection(url, user, password);
    }

	// 释放资源
    public static void close(ResultSet rs, Statement statement, Connection connection) {
        if (rs != null) {
            try {
                rs.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }

        if (statement != null) {
            try {
                statement.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
        if (connection != null) {
            try {
                connection.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
    }
}
```



## Jdbc控制事务

- 开启事务：`setAutoCommit(boolean autoCommit)`

  > 在执行sql之前开启事务

- 提交事务：`commit()` 

  >  当所有sql都执行完提交事务

- 回滚事务：`rollback()` 

  > 在catch中回滚事务

```java
public class demo_transaction {
    public static void main(String[] args) {
        PreparedStatement ps1 = null;
        PreparedStatement ps2 = null;
        Connection conn = null;
        try {
            // 从工具类中获取连接对象
            conn = JdbcUtils.getConnection();
            // 开启事务
            conn.setAutoCommit(false);
            String sql1 = "update student set score = score + ? where id = ?";
            String sql2 = "update student set score = score - ? where id = ?";
            ps1 = conn.prepareStatement(sql1);
            ps2 = conn.prepareStatement(sql2);
            // 设置参数
            ps1.setInt(1, 20);
            ps1.setInt(2, 1);
            ps2.setInt(1, 20);
            ps2.setInt(2, 2);

            ps1.executeUpdate();
            // 手动制造异常
            int i = 3 / 0;
            ps2.executeUpdate();
            // 提交事务
            conn.commit();
        } catch (Exception throwables) {
            // 事务回滚
            try {
                if (conn != null) {
                    conn.rollback();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
            throwables.printStackTrace();

        } finally {
            demo_Jdbc_utils.close(null, ps1, conn);
            demo_Jdbc_utils.close(null, ps2, null);
        }
    }
}
```





# 数据库连接池🍊

## 概念

数据库连接池，就是一个存放数据库连接对象的容器。

当系统初始化后，容器被创建，容器中会申请一些连接对象，当用户来访问数据库时，从容器中获取连接对象，用户访问完之后，会将连接对象归还给容器。

数据库连接池的优点：

- 节约资源
- 用户访问高效



## 数据库连接池的实现

标准接口：`javax.sql.DataSource`

获取连接：`getConnection()`

归还连接：`Connection.close()`

> 如果连接对象 Connection 是从连接池中获取的，那么调用Connection.close()方法，则不会再关闭连接，而是归还连接

数据库连接池的具体实现

- C3P0：经典数据库连接池技术
- Druid：阿里巴巴提供的数据库连接池实现技术



## C3P0

### 配置

c3p0 的配置文件是位于类路径下的 `c3p0-config.xml`

```xml
<c3p0-config>
    <!-- 使用默认的配置读取连接池对象 -->
    <default-config>
        <!--  连接参数 -->
        <property name="driverClass">com.mysql.jdbc.Driver</property>
        <property name="jdbcUrl">jdbc:mysql://localhost:3306/spring_learn</property>
        <property name="user">root</property>
        <property name="password">kzqkzq</property>

        <!-- 连接池参数 -->
        <property name="initialPoolSize">5</property>  <!-- 初始化申请的连接数量 -->
        <property name="maxPoolSize">10</property>
        <property name="checkoutTimeout">3000</property>
    </default-config>

    <named-config name="otherc3p0">
        <!--  连接参数 -->
        <property name="driverClass">com.mysql.jdbc.Driver</property>
        <property name="jdbcUrl">jdbc:mysql://localhost:3306/mybatis_learn</property>
        <property name="user">root</property>
        <property name="password">kzqkzq</property>

        <!-- 连接池参数 -->
        <property name="initialPoolSize">5</property>
        <property name="maxPoolSize">8</property>
        <property name="checkoutTimeout">1000</property>
    </named-config>
</c3p0-config>
```



### 使用步骤

步骤：

1. 导入jar包：
   - `c3p0-0.9.5.2.jar`
   - `mchange-commons-java-0.2.12.jar`
2. 定义配置文件
   - 名称：`c3p0.properties`  或  `c3p0-config.xml`
   - 路径：放在src目录下会**自动加载配置文件**

3. 获取数据库连接池对象：`ComboPooledDataSource`
4. 获取连接： `getConnection`

```java
public class demo_01_c3p0 {
    public static void main(String[] args) throws SQLException {
        // 创建数据库连接池对象（使用默认配置）
        DataSource ds = new ComboPooledDataSource();
        
        // 使用其他配置
        // DataSource ds = new ComboPooledDataSource("otherc3p0");    
        
        // 获取连接对象
        Connection conn = ds.getConnection();
        // 打印
        System.out.println(conn);
    }
}
```





## Druid

### 配置

Druid 的配置文件是位于类路径下的 `druid.properties`

```properties
driverClassName=com.mysql.jdbc.Driver
url=jdbc:mysql://127.0.0.1:3306/school?characterEncoding=utf-8
username=root
password=kzqkzq
# 初始化连接数量
initialSize=5
# 最大连接数
maxActive=10
# 最大超时时间
maxWait=3000
```



### 使用步骤

1. 导入jar包：`druid-1.0.9.jar`
2. 定义配置文件：`druid.properties`

3. 加载配置文件
4. 通过工厂类获取获取数据库连接池对象：`DruidDataSourceFactory`
5. 获取连接：`getConnection`

```java
public class demo_01 {
    public static void main(String[] args) throws Exception {
        Properties prop = new Properties();
        InputStream is = demo_01.class.getClassLoader().getResourceAsStream("druid.properties");
        prop.load(is);
        // 通过 DruidDataSourceFactory 工厂类创建数据库连接池
        DataSource ds = DruidDataSourceFactory.createDataSource(prop);
      	// 获得连接对象
        Connection conn = ds.getConnection();
        System.out.println(conn);
        conn.close();
    }
}
```



### DruidUtils

创建 Druid 数据库连接池的工具类：

```java
public class DruidUtils {
    private static DataSource ds;

    static {
        try {
            // 加载配置文件
            Properties prop = new Properties();
            ClassLoader loader = demo_02_Utils.class.getClassLoader();
            prop.load(loader.getResourceAsStream("druid.properties"));
            // 获取DataSource
            ds = DruidDataSourceFactory.createDataSource(prop);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /**
     * 获取数据库连接池对象
     * @return
     */
    public static DataSource getDataSource() {
        return ds;
    }

    /**
     * 获取连接对象
     * @return
     * @throws SQLException
     */
    public static Connection getConnection() throws SQLException {
        return ds.getConnection();
    }


    public static void close(Statement stmt, Connection conn) {
        close(null, stmt, conn);
    }

    public static void close(ResultSet rs, Statement stmt, Connection conn) {
        if (rs != null) {
            try {
                rs.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
        if (stmt != null) {
            try {
                stmt.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
        if (conn != null) {
            try {
                conn.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
    }
}
```



使用数据库连接池工具类：

```java
public class demo {
    public static void main(String[] args) {
        Connection conn = null;
        PreparedStatement ps = null;
        try {
            conn = DruidUtils.getConnection();
            String sql = "insert into student values(null, ?, ?, ?)";
            ps = conn.prepareStatement(sql);
            ps.setString(1, "帕斯卡");
            ps.setDouble(2, 18);
            ps.setInt(3, 0);
            System.out.println(ps);
            int result = ps.executeUpdate();
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        } finally {
            DruidUtils.close(ps, conn);
        }
    }
}
```







# DBUtils🍊

DBUtils简化了Jdbc的开发步骤，使得我们可以用更少量的代码实现连接数据库的功能。

DBUtils封装了Jdbc的操作，核心功能如下：

Dbutils三个核心功能介绍

1. `QueryRunner`：提供对 sql 语句操作的 API.
2. `ResultSetHandler` ：接口，用于定义select操作后，怎样封装结果集.
3. `DbUtils` ：工具类，定义了关闭资源与事务处理的方法







