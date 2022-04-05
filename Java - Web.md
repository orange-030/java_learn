# Java Web🧀

## Http服务器

HTTP服务器本质上也是一种应用程序，它通常运行在服务器之上，绑定服务器的IP地址并监听某一个tcp端口来接收并处理HTTP请求，这样客户端（一般来说是IE，Firefox，Chrome等浏览器）就能够通过HTTP协议来获取服务器上的网页、文档、音频、视频等等资源。

Apache是指Apache软件基金会下的一个项目：[Apache HTTP Server Project](https://httpd.apache.org/)，是一个HTTP服务器。



## 静态资源和动态资源

**静态资源：**客户端发送请求到web服务器，web服务器从内存在取到相应的文件，返回给客户端，客户端解析并渲染显示出来。

**动态资源：**客户端请求的动态资源，先将请求交于web容器，web容器连接数据库，数据库处理数据之后，将内容交给web服务器，web服务器返回给客户端解析渲染处理。





## Tomcat

[Apache Tomcat](http://tomcat.apache.org/) 是一个免费的开源的Web应用服务器，属于轻量级应用服务器，与Apache HTTP Server 相比，Tomcat 能够将动态资源返回到客户端，而 Apache HTTP Server 和 Nginx 用于返回静态资源。

Apache HTTP Server 和 Nginx 本身不支持生成动态页面，但它们可以通过其他模块来支持（例如通过Shell、PHP、Python脚本程序来动态生成内容），如果想要使用 Java 程序来动态生成资源内容，使用 Apache HTTP Server 和 Nginx 则很难做到。

Java Servlet 技术以及衍生的 Java Server Pages 技术可以让 Java 程序也具有处理 HTTP 请求并且返回动态内容的能力，Tomcat 正是支持运行「Servlet / JSP」应用程序的容器（Container）

Tomcat 运行在 JVM 之上，它和 HTTP 服务器一样，绑定 IP 地址并监听 TCP 端口，同时还包含以下功能：

1. 管理 Servlet 程序的生命周期

2. 将 URL 映射到指定的Servlet进行处理

3. 与 Servlet 程序合作处理 HTTP 请求 —— 根据 HTTP 请求生成 HttpServletRequest 对象并传递给 Servlet 进行处理，将Servlet 中的 HttpServletResponse 对象生成的内容返回给浏览器

Tomcat 常和 Nginx 配合使用：

- 动静态资源分离：运用 Nginx 的反向代理功能分发请求，所有动态资源的请求交给 Tomcat，而静态资源的请求（图片、视频、CSS、JavaScript等）则直接由 Nginx 返回到浏览器，这样能大大减轻 Tomcat 的压力。

- 负载均衡：当业务压力增大时，可能一个 Tomcat 的实例不足以处理，那么这时可以启动多个 Tomcat 实例进行水平扩展，而 Nginx 的负载均衡功能可以把请求通过算法分发到各个不同的实例进行处理



# Tomcat🧀

## 文件目录

```markdown
- tomcat文件目录
bin：binary，可执行文件
conf：配置文件
libs：依赖的jar包
logs：日志文件
temp：临时文件
webapps：存放web项目
work：存放运行时的数据
```

启动：`bin/startup.bat`

访问：`http://127.0.0.1:8080`

## 项目部署

1. 直接将项目放到 webapps 目录下

	- 简化部署：将项目打成一个 war 包，再将 war 包放置到 webapps目录下
	- war 包会自动被解压缩

2. 配置 `conf/server.xml` 文件

   在 `<Host>` 标签体中配置 `<Context docBase="/../project" path="/contextPath" />` 

   - docBase：项目存放的路径

   - path：虚拟目录

3. 在 `conf/Catalina/localhost` 创建任意名称的 xml 文件

   在文件中编写`<Context docBase="/../project" />`

   - 虚拟目录：xml 文件的名称



## IDEA部署

```
Java动态项目的目录结构：
--- 项目的根目录
    --- WEB-INF目录
   	    --- web.xml：web项目的核心配置文件
   	    --- classes：放置字节码文件的目录
   	    --- lib：放置依赖的jar包
```

### war 

`war` 模式可以称为发布模式，将 web 工程先打成 war 包，再上传到服务器 

应用程序上下文绝对路径：`/Users/yorua/opt/apache-tomcat-8.5.60/webapps/example/`



### war exploded

将 web 工程以当前文件夹的位置关系上传到服务器

`war exploded` 模式是直接把文件夹、jsp页面 、classes 等移到 Tomcat 部署文件夹中，进行加载部署。因此这种方式支持热部署，一般在开发的时候也是用这种方式。

应用程序上下文绝对路径：`/Users/yorua/Project/IdeaProject/SpringMVC/example/target/example/`



# Servlet🧀

Servlet（Server Applet）：小服务程序或服务连接器，是用Java编写的服务器端程序。

## web.xml

`web.xml` 作用是初始化配置信息。

启动一个 web 项目的时候，web 容器（tomcat）会去读取它的配置文件 `web.xml `，读取 `<listener>` 和 `<context-param>`两个节点。 

web 工程加载顺序：

```
ServletContext -> context-param -> listener -> filter -> servlet
```



```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
  
    <!--配置servlet-->
    <servlet>
        <servlet-name>demo1</servlet-name>
        <servlet-class>web.Servlet.ServletDemo01</servlet-class>
    </servlet>

    <servlet-mapping>
        <servlet-name>demo1</servlet-name>
        <url-pattern>/demo1</url-pattern>
    </servlet-mapping>
</web-app>
```

执行原理：

1. 当服务器接受到客户端浏览器的请求后，会解析请求URL路径，获取访问的Servlet的资源路径

2. 查找 `web.xml` 文件，是否有对应的`<url-pattern>`标签体内容。
3. 如果有，则在找到对应的 `servlet-name`，再找到 `<servlet-class>` 全类名
4. Tomcat 会将字节码文件加载进内存，并且创建其对象
5. 调用该对象的方法



## @WebServlet

Servlet 3.0 支持注解配置，无需再配置 `web.xml`

```java
@WebServlet(urlPatterns = "/demo1")
public class ServlectDemo01 implements Servlet {
  // ...
}
```



<img src="http://store.secretcamp.cn/uPic/image-20210529144705177202105291447051622270825HjouhUHjouhU.png" alt="image-20210529144705177" style="zoom:50%;" />





## IDEA集成Tomcat

IDEA 会为每一个 tomcat 部署的项目单独建立一份配置文件

项目分为工作空间项目和 tomcat 部署的 web 项目，tomcat 真正访问的是 "tomcat部署的web项目"，它对应了 "工作空间项目" 的 web 目录下的所有资源。



## Servlet继承

- `GenericServlet`：将 Servlet 接口中其他的方法做了默认空实现，只将 `service()` 方法作为抽象。

- `HttpServlet`：对 http 协议的一种封装，简化操作

```
Servlet（接口） ————> GenericServlet（抽象类） ————> HttpServlet（抽象类）
```





## ServletContext对象

ServletContex代表整个web应用，可以和程序的容器（服务器）来通信

获取：

- `request.getServletContext()`：通过request对象获取
- `this.getServletContext()`：通过HttpServlet获取

功能：

- `String getMimeType(String file)`：获取MIME类型
- `setAttribute(String name,Object value)`：设置共享数据
- `getAttribute(String name)`：获取共享数据
- `removeAttribute(String name)`：移除共享数据
- `String getRealPath(String path)`：获取文件的真实（服务器）路径  



# Request🧀

请求消息：客户端发送给服务器端的数据



## 对象继承体系结构

```
ServletRequest（接口） ————> HttpServletRequest（接口） ————> org.apache.catalina.connector.RequestFacade（tomcat提供的实现类）
```



## 获取请求消息数据

- `String getMethod()`：获取请求方式
- `String getContextPath()`：获取虚拟目录
- `String getServletPath()`：获取Servlet路径
- `String getQueryString()`：获取get方式请求参数
- `String getRequestURI()`：获取请求URI
- `StringBuffer getRequestURL()`：获取请求URL
- `String getProtocol()`：获取协议及版本
- `String getRemoteAddr()`：获取客户机的IP地址



## 获取请求头数据

- `String getHeader(String name)`：通过请求头的名称获取请求头的值
- `Enumeration<String> getHeaderNames()`：获取所有的请求头名称



## 获取请求体数据

POST请求才有请求体

1. 当请求体类型是 `application/x-www-form-urlencoded` 时，以下两种方式可以读取数据。

   - `BufferedReader getReader()`：获取字符输入流，只能操作字符数据，是 `getInputStream` 返回内容的封装

   - `ServletInputStream getInputStream()`：获取字节输入流，可以操作所有类型数据，多用于文件的上传

2. 当请求体类型是 `multipart/form-data` 或 `application/json` 时，以下四种方式可以读取数据。

   - `String getParameter(String name)`：根据参数名称获取参数值，用于获取单个参数的单个值

   - `String[] getParameterValues(String name)`：根据参数名称获取参数值的数组，用于获取单个参数的多个值

   - `Enumeration<String> getParameterNames()`：获取所有请求参数的名称

   - `Map<String,String[]> getParameterMap()`：获取所有参数的map集合



## 设置输入流相关信息

- `request.setCharacterEncoding("utf-8")`：设置输入流的编码

针对 POST 请求，用指定的编码集去覆盖 request 对象中的默认的"ISO-8859-1"编码集，request.getParameter 方法就会用新的编码集去解码



## 请求转发

请求转发：一种在服务器内部的资源跳转方式

域对象：一个有作用范围的对象，可以在范围内共享数据

request域：代表一次请求的范围，一般用于请求转发的多个资源中共享数据

- `RequestDispatcher getRequestDispatcher(String path)`：通过request对象获取请求转发器对象：
- `forward(ServletRequest request, ServletResponse response)`：使用RequestDispatcher对象来进行转发
- `void setAttribute(String name,Object obj)`:存储数据

- `Object getAttitude(String name)`：通过键获取值

- `void removeAttribute(String name)`：通过键移除键值对









# Response🧀

响应消息：服务器端发送给客户端的数据



## 设置响应头

- 设置响应头：``response.setHeader(String name, String value)`

- 设置编码：`response.setContentType("text/html;charset=utf-8");`



## 设置响应体

- 获取字符输出流：`PrintWriter getWriter()`

- 获取字节输出流：`ServletOutputStream getOutputStream()`



## 设置输出流相关信息

- 重定向：`response.sendRedirect("/response/demo")`

- 设置编码：`response.setCharacterEncoding("utf-8")`

- 设置 MIME 类型：`response.setContentType("text/html;charset=utf-8")`

  > 告诉浏览器数据的 MIME 类型，根据不同的 MIME 调用浏览器内不同的程序嵌入模块来处理相应的数据



## 重定向

```java
// way1
response.setStatus(302);
response.setHeader("location","/response/demo");

// way2
response.sendRedirect("/response/demo");
```



* 重定向的特点：redirect
  1. 地址栏发生变化
  2. 重定向可以访问其他站点(服务器)的资源
  3. 重定向是两次请求，不能使用request对象来共享数据
* 转发的特点：forward
  1. 转发地址栏路径不变
  2. 转发只能访问当前服务器下的资源
  3. 转发是一次请求，可以使用request对象来共享数据





# Cookie🧀

会话：一次会话中包含多次请求和响应，浏览器第一次给服务器资源发送请求，会话建立，直到有一方断开为止。

Cookie：客户端会话技术，将数据保存到客户端，用于在一次会话的范围内的多次请求间共享数据，由 name、value 和其它几个用于控制 Cookie 有效期、安全性、使用范围的可选属性组成。

Cookie的特点：

- 大小不超过 4 kb
- 同一个域名下的总 cookie 数量一般不超过 20 个



## 原理

Cookie技术基于响应头中 `set-cookie` 字段和请求头中 `cookie` 字段实现



## 方法

- `new Cookie(String name, String value)`：创建Cookie

- `response.addCookie(Cookie cookie)`：设置Cookie

- `setMaxAge(int seconds)`：设置Cookie的生命周期（秒）

  - 正数：将Cookie数据写到硬盘的文件中，持久化存储
  - 负数：默认值（关闭浏览器后清除Cookie）
  - 零：删除cookie信息

- `setPath(String path)`：设置 cookie 的获取范围，默认情况下，设置当前的虚拟目录

  如果要共享，则可以将 path 设置为 `"/"`

- `setDomain(String path)`：如果设置一级域名相同，那么多个服务器之间cookie可以共享

  如果设置`setDomain(".baidu.com")`，那么tieba.baidu.com和news.baidu.com中cookie可以共享



# Session🧀

Session：服务器端会话技术，在一次会话的多次请求间共享数据，将数据保存在服务器端的对象中

 1. session用于存储一次会话的多次请求的数据，存在服务器端
 2. session可以存储任意类型，任意大小的数据



## 原理

Session 的实现依赖于 Cookie



## 方法

- `HttpSession session = request.getSession()`：获取HttpSession对象
- `Object getAttribute(String name)`：获取属性 
- `void setAttribute(String name, Object value)`：设置属性
- `void removeAttribute(String name)`：移除属性



## QA

1. 当客户端关闭后，服务器不关闭，两次获取 session 是否为同一个？

   默认情况下，不是。
   如果需要相同，则可以创建 Cookie，键为 JSESSIONID，设置最大存活时间，让 cookie 持久化保存。

   ```java
   Cookie cookie = new Cookie("JSESSIONID",session.getId());
   cookie.setMaxAge(60 * 60);
   response.addCookie(cookie);
   ```

   



2. 客户端不关闭，服务器关闭后，两次获取的session是同一个吗？

   不是同一个，但是要确保数据不丢失，tomcat可以自动完成以下工作

   - Session的钝化：在服务器正常关闭之前，将 session 对象系列化到硬盘上

   - Session的活化：在服务器启动后，将 session 文件转化为内存中的 session 对象即可



3. Session什么时候被销毁？

   - 服务器关闭

   - session对象调用 `invalidate()` 

   - session默认失效时间30分钟

   ```xml
   <!-- 修改 web.xml，自定义session的销毁时间 -->
   <session-config>
      <session-timeout>30</session-timeout>
   </session-config>
   ```

   

4. Session与Cookie的区别：
   - Session 存储数据在服务器端，Cookie 在客户端

   - Session 没有数据大小限制，Cookie 有

   - Session 数据安全，Cookie 相对于不安全





# Filter🧀





# Listener🧀





# JSP🧀

JSP： java服务器端页面（Java Server Pages）

可以理解为：一个特殊的页面，其中既可以指定定义 html 标签，又可以定义 java 代码



## JSP的原理

JSP本质上就是一个Servlet

```
index.jsp -> _index_jsp.java -> _index_jsp.class（servlet）
```

1. 服务器解析请求消息，找是否有 `index.jsp` 资源。
2. 如果找到了，会将 `index.jsp` 转换为 `Java` 文件
3. 编译 `java` 文件，生成 `Class` 字节码文件
4. 由字节码文件提供访问



## JSP的脚本语法

```java
<%  代码 %>：代码在jsp转换后的java类的service方法中，service方法中可以定义什么，该脚本中就可以定义什么。
<%! 代码 %>：代码在jsp转换后的java类的成员位置。
<%= 代码 %>：代码会输出到页面上。输出语句中可以定义什么，该脚本中就可以定义什么。
```



## JSP指令

JSP指令的作用：用于配置JSP页面，导入资源文件

格式：`<%@ 指令名称 属性名1=属性值1 属性名2=属性值2 ... %>`



### page

- contentType：等同于response.setContentType()
  - 设置响应体的MIME类型以及字符集
  - 设置当前jsp页面的编码（只能是高级的IDE才能生效，如果使用低级工具，则需要设置pageEncoding属性设置当前页面的字符集）
- import：导包
- errorPage：当前页面发生异常后，会自动跳转到指定的错误页面
- isErrorPage：标识当前也是是否是错误页面。
  - true：是，可以使用内置对象exception
  - false：否，默认值，不可以使用内置对象exception



### include

导入页面的资源文件

```java
<%@include file="head.jsp"%>
<%@include file="tail.jsp"%>
```



### taglib

导入资源

```java
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
  prefix：前缀  可以自定义
```



## JSP的内置对象

内置对象：在jsp页面中不需要获取和创建，可以直接使用的对象



### pageContent

真实类型：PageContext

当前页面共享数据，还可以获取其他八个内置对象



### request

真实类型：HttpServletRequest

一次请求访问的多个资源(转发)



### response

真实类型：HttpServletResponse

响应对象



### out

真实类型：JspWriter

字符输出流对象，可以将数据输出到页面上。

`out` 和 `response.getWriter()` 类似，但也有区别：

- 在tomcat服务器真正给客户端做出响应之前，会先找response缓冲区数据，再找out缓冲区数据
- response.getWriter()数据输出永远在out.write()之前



### session

真实类型：HttpSession

一次会话的多个请求间共享数据



### application

真实类型：ServletContext

所有用户间共享数据



### page

真实类型：Object

当前页面（Servlet）的对象  this



### config

真实类型：ServletConfig

Servlet的配置对象



### exception

真实类型：Throwable

异常对象

只有声明 isErrorPage=true 才可以使用



## JSP注释

1. html注释：
   `<!-- -->`：只能注释html代码片段
2. jsp注释：推荐使用
   `<%-- --%>`：可以注释所有代码片段



# EL表达式🧀

EL：Expression Language 表达式语言



## 语法

**语法：** `${表达式}`

`\${表达式}`：在EL表达式前加一个 `\` 表示忽略EL表达式



### 运算符

EL表达式支持算数、比较、逻辑、空运算符

```javascrip
${empty list}: 判断字符串、集合、数组对象是否为null或者长度为0
${not empty str}: 判断字符串、集合、数组对象是否不为null 并且 长度>0
```



### 获取值

1. `${域名称.键名}`：从指定域中获取指定键的值

| 域名称           | 对象名称    |
| ---------------- | ----------- |
| pageScope        | pageContext |
| requestScope     | request     |
| sessionScope     | session     |
| applicationScope | application |

2. `${键名}`：表示依次从最小的域中查找是否有该键对应的值，直到找到为止。

3. 获取对象

   `${域名称.键名.属性名}`：本质上会去调用对象的getattr方法

4. 获取List集合

   `${域名称.键名[索引]}`

5. 获取Map集合的值

    `${域名称.键名.key名称}` or `${域名称.键名["key名称"]}`



## 隐式对象

EL 表达式中有 11 个隐式对象

pageContext：可以获取其他8个内置对象

```
${pageContext.request.contextPath}：动态获取虚拟目录
```



# JSTL🧀

## 概念

JSTL（JavaServer Pages Tag Library）： JSP 标准标签库

作用：替换和简化 jsp 页面中 java 代码的编写



## 使用步骤

1. 导入 jstl 相关 jar 包

   - `javax.servlet.jsp.jstl.jar`

   - `jstl-impl.jar`

2. 引入标签库

   taglib指令 `<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>`

3. 使用标签



## 常用标签

### if

相当于java代码的if语句

- test 必须属性，接受boolean表达式
- if标签没有else情况，想要else情况，则可以再定义一个 `<c:if>` 标签

```jsp
<%
    request.setAttribute("condition", "true");
%>

<c:if test="${requestScope.condition}">
    <h1> this is true</h1>
</c:if>
```



### choose

相当于java代码的switch语句

- 使用choose标签声明 -> 相当于switch声明
- 使用when标签做判断 -> 相当于case
- 使用otherwise标签做其他情况的声明 -> 相当于default

```jsp
<%
    request.setAttribute("number", 5);
%>

<c:choose>
    <c:when test="${number == 1}">星期一</c:when>
    <c:when test="${number == 2}">星期二</c:when>
    <c:when test="${number == 3}">星期三</c:when>
    <c:when test="${number == 4}">星期四</c:when>
    <c:when test="${number == 5}">星期五</c:when>
    <c:when test="${number == 6}">星期六</c:when>
    <c:when test="${number == 7}">星期天</c:when>
    <c:otherwise>日期错误</c:otherwise>
</c:choose>
```



### foreach

相当于java代码的for语句

```jsp
<%
    List<String> list = new ArrayList<>();
    list.add("aaa");
    list.add("bbb");
    list.add("ccc");
    request.setAttribute("list", list);
%>

// 普通for循环   
<c:forEach begin="0" end="10" var="i" step="2" varStatus="s">
    ${i} == ${s.current} == ${s.index}  循环次数：${s.count}<br>
</c:forEach>

// for循环遍历容器   
<c:forEach items="${list}" var="item" varStatus="s">
    ${item} == ${s.current}  索引：${s.index}   循环次数：${s.count}  <br>
</c:forEach>
```



























