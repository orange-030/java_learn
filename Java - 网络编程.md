# 网络编程入门

## 软件结构

- C/S结构 ：全称为 Client/Server 结构，是指客户端和服务器结构。常见程序有ＱＱ、迅雷等软件。

- B/S结构 ：全称为 Browser/Server 结构，是指浏览器和服务器结构。常见浏览器有谷歌、火狐等。

两种架构各有优势，但是无论哪种架构，都离不开网络的支持。网络编程，就是在一定的协议下，实现两台计算机的通信的程序。



# TCP通信程序

## 概述

TCP通信能实现两台计算机之间的数据交互，通信的两端，要严格区分为客户端（Client）与服务端（Server）。

两端通信时步骤：

1. 服务端程序，需要事先启动，等待客户端的连接。
2. 客户端主动连接服务器端，连接成功才能通信。服务端不可以主动连接客户端。

在 Java 中，提供了两个类用于实现 TCP 通信程序：

1. 客户端：`java.net.Socket` 类表示。创建 `Socket` 对象，向服务端发出连接请求，服务端响应请求，两者建立连接开始通信。
2. 服务端：`java.net.ServerSocket` 类表示。创建 `ServerSocket` 对象，相当于开启一个服务，并等待客户端的连接。



## Socket类  

TCP通信的客户端：向服务器发送连接请求，给服务器发送数据，读取服务器回写的数据

`java.net.Socket` 类：该类实现客户端套接字，套接字指的是两台设备之间通讯的端点，是包含了IP地址和端口号的网络单位



### 构造方法

* `public Socket(String host, int port)` :创建套接字对象并将其连接到指定主机上的指定端口号。如果指定的host是null ，则相当于指定地址为回送地址。  

  > 小贴士：回送地址(127.x.x.x) 是本机回送地址（Loopback Address），主要用于网络软件测试以及本地机进程间通信，无论什么程序，一旦使用回送地址发送数据，立即返回，不进行任何网络传输。

构造举例，代码如下：

```java
Socket client = new Socket("127.0.0.1", 6666);
```



### 成员方法

* `public InputStream getInputStream()` ： 返回此套接字的输入流。
  * 如果此 Socket 具有相关联的通道，则生成的 InputStream 的所有操作也关联该通道。
  * 关闭生成的 InputStream 也将关闭相关的 Socket 。
* `public OutputStream getOutputStream()` ： 返回此套接字的输出流。
  * 如果此 Socket 具有相关联的通道，则生成的 OutputStream 的所有操作也关联该通道。
  * 关闭生成的 OutputStream 也将关闭相关的 Socket 。
* `public void close()` ：关闭此套接字。
  * 一旦一个 Socket 被关闭，它不可再使用。
  * 关闭此 Socket 也将关闭相关的 InputStream 和 OutputStream 。 
* `public void shutdownOutput()` ： 禁用此套接字的输出流。   
  * 任何先前写出的数据将被发送，随后终止输出流。 



### 实现方法

实现步骤：
1. 创建一个客户端对象 `Socket`，构造方法绑定服务器的IP地址和端口号

2. 使用Socket对象中的方法 `getoutputStream()` 获取网络字节输出流 `OutputStream` 对象

3. 使用网络字节输出流 `OutputStream` 对象中的方法`write()`，给服务器发送数据

4. 使用Socket对象中的方法 `getInputStream()` 获取网络字节输入流 InputStream 对象

5. 使用网络字节输入流 `InputStream` 对象中的方法 `read` ，读取服务器回写的数据
6. 释放资源（`Socket`）

> - 客户端和服务端进行交互，必须使用Socket中提供的网络流
>
> - 创建客户端对象时，就会请求服务器，和服务器进行三次握手建立连接通路



## ServerSocket类

`ServerSocket` 类：这个类实现了服务器套接字，该对象等待通过网络的请求。



### 构造方法

* `public ServerSocket(int port)` ：使用该构造方法在创建ServerSocket对象时，就可以将其绑定到一个指定的端口号上，参数port就是端口号。

  构造举例，代码如下：

   ```java
   ServerSocket server = new ServerSocket(6666);
   ```



### 成员方法

* `public Socket accept()` ：侦听并接受连接，返回一个新的Socket对象，用于和客户端实现通信。该方法会一直阻塞直到建立连接。 



### 实现方法

服务器的实现步骤：
1. 创建服务器 `Serversocket` 对象和系统要指定的端口号
2. 使用 `ServerSocket` 对象中的方法 `accept()`，获取到请求的客户端对象 `Socket`
3. 使用 `Socket` 对象中的方法 `getInputStream()` 获取网络字节输入流 `InputStream` 对象
4. 使用网络字节输入流 `InputStream` 对象中的方法 `read()` ，读取客户端发送的数据
5. 使用 `Socket` 对象中的方法`getoutputStream() `获取网络字节输出流 `OutputStream` 对象
6. 使用网络字节输出流 `OutputStream` 对象中的方法 `write()`，给客户端回写数据
7. 释放资源（`Socket` ，`ServerSocket`）



## 简单的TCP网络程序	

### TCP通信分析

1. 【服务端】启动，创建 `ServerSocket` 对象，等待连接。
2. 【客户端】启动，创建 `Socket` 对象，请求连接。
3. 【服务端】接收连接，调用 `accept` 方法，并返回一个 `Socket` 对象。
4. 【客户端】`Socket` 对象，获取 `OutputStream` ，向服务端写出数据。
5. 【服务端】`Scoket` 对象，获取 `InputStream` ，读取客户端发送的数据。

> 到此，客户端向服务端发送数据成功。

![](Image\Java\TCP通信_01.jpg)

6. 【服务端】`Socket` 对象，获取 `OutputStream` ，向客户端回写数据。
7. 【客户端】`Socket` 对象，获取 `InputStream` ，解析服务端回写的数据。
8. 【客户端】释放资源，断开连接。

### 连接实现

服务端的实现

```java
public class TCPServer {
    public static void main(String[] args) throws IOException {
        ServerSocket serverSocket = new ServerSocket(8088);
        Socket socket = serverSocket.accept();
        InputStream is = socket.getInputStream();
        byte[] bytes = new byte[1024];
        int len = is.read();
        System.out.println(new String(bytes, 0, len));
        OutputStream os = socket.getOutputStream();
        os.write("hello client".getBytes());
        socket.close();
        serverSocket.close();

    }
}
```

客户端的实现

```java
public class TCPClient {
    public static void main(String[] args) throws IOException {
        Socket socket = new Socket("127.0.0.1", 8088);
        OutputStream os = socket.getOutputStream();
        os.write("hello server".getBytes());
        InputStream is = socket.getInputStream();
        byte[] bytes = new byte[1024];
        int len = is.read(bytes);
        System.out.println(new String(bytes, 0, len));
        socket.close();
    }
}
```



# 文件上传

## 文件上传原理

TCP通信的文件上传案例
原理：客户端读取本地的文件，把文件上传到服务器，服务器在把上传的文件保存到服务器的硬盘上

1. 客户端使用本地的字节输入流，读取要上传的文件

2. 客户端使用网络字节输出流，把读取到的文件上传到服务器

3. 服务器使用网络字节输入流，读取客户端上传的文件

4. 服务器使用本地字节输出流，把读取到的文件，保存到服务器的硬盘上

5. 服务器使用网络字节输出流，给客户端回写一个”上传成功”

6. 客户端使用网络字节输入流，读取服务器回写的数据

7. 释放资源

  注意：
  客户端和服务器和本地硬盘进行读写，需要使用自己创建的字节流对象（本地流）
  客户端和服务器之间进行读写，必须使用Socket中提供的字节流对象（网络流）

## 实现方法

![](Image\Java\upload02.jpg)

### 实现步骤

服务端的实现

1. 创建一个服务器 `Serversocket` 对象，和系统要指定的端口号
2. 使用 `ServerSocket` 对象中的方法 `accept` ，获取到请求的客户端 `Socket` 对象
3. 使用 `Socket` 对象中的方法 `getInputStream` ，获取到网络字节输入流 `InputStream` 对象
4. 判断 upload 文件夹是否存在，不存在则创建
5. 创建一个本地字节输出流 `FileOutputStream` 对象，构造方法中绑定要输出的目的地
6. 使用网络字节输入流 `InputStream` 对象中的方法 `read` ，读取客户端上传的文件
7. 使用本地字节输出流 `FileOutputStream` 对象中的方法write，把读取到的文件保存到服务器的硬盘上
8. 使用 `Socket` 对象中的方法 `getOutputStream` ，获取到网络字节输出流 `OutputStream` 对象
9. 使用网络字节输出流 `OutputStream` 对象中的方法 `write` ，给客户端回写“上传成功“
10. 释放资源（`FileOutputStream`、 `Socket`、 `ServerSocket`）

客户端的实现

1. 创建一个本地字节输入流 `FileInputStream` 对象，构造方法中绑定要读取的数据源
2. 创建一个客户端 `Socket` 对象，构造方法中绑定服务器的IP地址和端口号
3. 使用 `Socket` 中的方法 `getOutputStream` ，获取网络字节输出流 `OutputStream` 对象
4. 使用本地字节输入流 `FileInputStream` 对象中的方法 `read` ，读取本地文件
5. 使用网络字节输出流 `OutputStream` 对象中的方法 `write` ，把读取到的文件上传到服务器
6. 使用 `Socket` 中的方法 `getInputStream` ，获取网络字节输入流 `InputStream` 对象
7. 使用网络字节输入流 `InputStream` 对象中的方法 `read` 读取服务回写的数据
8. 释放资源（`FiLeInputStream` 、`Socket`）



### 基本实现

服务端的实现

```java
public class TCPServer {
    public static void main(String[] args) throws IOException {
        // 创建服务器套接字
        ServerSocket serverSocket = new ServerSocket(8088);
        while (true) {
            // 获取客户端套接字
            Socket socket = serverSocket.accept();
            // 创建网络输入流，获取客户端写入的数据
            InputStream is = socket.getInputStream();
            File file = new File("upload");
            if (!file.exists()) {
                file.mkdir();
            }
            // 自定义一个命名规则
            String fileName = "upload" + System.currentTimeMillis() + new Random().nextInt(10000);
            // 创建本地输出流，将数据写入本地持久化
            FileOutputStream fos = new FileOutputStream(file + "/" + fileName + ".jpg");
            int len = 0;
            byte[] bytes = new byte[1024];
            while ((len = is.read()) != -1) {
                fos.write(bytes, 0, len);
            }
            // 获取网络输出流，向客户端回写数据
            socket.getOutputStream().write("上传成功".getBytes());
            is.close();
            fos.close();
            socket.close();
        }
    }
}
```



客户端的实现

```java
public class TCPClient {
    public static void main(String[] args) throws IOException {
        Socket socket = new Socket("127.0.0.1", 8088);
        // 使用网络输出流，向服务器写入数据
        OutputStream os = socket.getOutputStream();
        // 创建本地输入流，读取本地数据
        FileInputStream fis = new FileInputStream("river.jpg");
        int len = 0;
        byte[] bytes = new byte[1024];
        // 当读取到-1时，不会再写入，如果不告知服务器写完了则会引发阻塞
        while ((len = fis.read(bytes)) != -1) {
            os.write(bytes, 0, len);  
        }
        socket.shutdownOutput();  // 告诉服务器 -> 写完了
        // 获取网络输入流，获得服务器回写的数据
        InputStream is = socket.getInputStream();
        while ((len = is.read()) != -1) {
            System.out.println(new String(bytes, 0, len));
        }
        fis.close();
        socket.close();
    }
}
```


