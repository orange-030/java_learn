

# I/O概述🍭

I/O（Input / Output）

在 Java 程序中，对于数据的输入/输出操作以 “流”（stream）的方式进行；

以下是 `Java.io` 包中最重要的就是 5 个类和 3 个接口

| 类           | 说明       |
| ------------ | ---------- |
| File         | 文件类     |
| InputStream  | 字节输入流 |
| OutputStream | 字节输出流 |
| Reader       | 字节输入流 |
| Writer       | 字节输出流 |
| Closeable    | 关闭流接口 |
| Flushable    | 刷新流接口 |
| Serializable | 序列化接口 |



## I/O分类

按数据分类：

- 字节流：按照字节读取数据（InputStream、OutputStream）
- 字符流：按照字符读取数据（Reader、Writer），因为文件编码的不同，从而有了对字符进行高效操作的字符流对象。原理：底层还是基于字节流操作，自动搜寻了指定的码表。

按功能分类：

- 节点流：可以直接从数据源或目的地读写数据

- 处理流：不直接连接到数据源或目的地，是其他流进行封装。目的主要是简化操作和提高性能。

节点流和处理流的关系：

①节点流处于 I/O 操作的第一线，所有操作必须通过他们进行；

②处理流可以对其他流进行处理（提高效率或操作灵活性）。



## 字符集

- 编码（encode）：字节 → 字符

- 解码（decode）：字符 → 字节

```java
// 字符 ---> 字节流
String msg = "字节&字符";
byte[] data = msg.getBytes("utf-8");
System.out.println(data.length);  // 13

// 字节流 ---> 字符
msg = new String(data, 0, data.length, "unicode");
System.out.println(msg);
```



## 四个抽象父类

| 抽象类         | 说明                               | 常用方法                                           |
| -------------- | ---------------------------------- | -------------------------------------------------- |
| `InputStream`  | 字节输入流的父类，数据单位为字节。 | int read()<br>void close()                         |
| `OutputStream` | 字节输出流的父类，数据单位为字节。 | void write(int)<br>void flush()<br>void close()    |
| `Reader`       | 字符输入流的父类，数据单位为字符。 | int read()<br>void close()                         |
| `Writer`       | 字符输出流的父类，数据单位为字符。 | void write(String)<br>void flush()<br>void close() |



## 数据储存原理

### 写入数据的原理

```
java程序 → JVM（java虚拟机）→ OS → OS调用写数据的方法 → 将数据写到文件中
```

### 读取数据的原理

```
磁盘 --> OS --> JVM（java虚拟机）--> java程序（内存）
```







# 字节流🍭

一切文件数据（文本、图片、视频等）在存储时，都是以二进制数字的形式保存，都是一个一个的字节。



## 字节输出流 OutputStream

`java.io.OutputStream `抽象类是表示字节输出流的所有类的超类，将指定的字节信息写出到目的地。它定义了字节输出流的基本共性功能方法。

* `public void close()` ：关闭此输出流并释放与此流相关联的任何系统资源。  
* `public void flush() ` ：刷新此输出流并强制任何缓冲的输出字节被写出。  
* `public void write(byte[] b)`：将 b.length字节从指定的字节数组写入此输出流。  
* `public void write(byte[] b, int off, int len)` ：从指定的字节数组写入 len字节，从偏移量 off开始输出到此输出流。  
* `public abstract void write(int b)` ：将指定的字节输出流。



## FileOutputStream类

`java.io.FileOutputStream ` 类是 `OutputStream` 的一个子类，用于将数据写出到文件。

### 流程

1. 创建一个FileOutputStream对象，构造方法中传递写入数据的目的地

2. 调用FileoutputStream对象中的方法write，把数据写入到文件中

3. 释放资源（流使用会占用一定的内存，使用完毕要把内存清空，提供程序的效率）

### 构造方法

* `FileInputStream(File file)`： 通过打开与实际文件的连接来创建一个 FileInputStream ，该文件由文件系统中的File对象命名。 
* `FileInputStream(String name)`： 通过打开与实际文件的连接来创建一个 FileInputStream ，该文件由文件系统中的路径名name命名。  

当创建一个流对象时，必须传入文件路径。该路径下如果没有该文件，会抛出 `FileNotFoundException` 

### 写出字节数据

**1. 写出字节**：`write(int b)` ，每次可以写出一个字节数据

```java
public class IO_fos_01 {
    public static void main(String[] args) throws IOException {
        String path = System.getProperty("user.dir");
        FileOutputStream fos = new FileOutputStream(path + "/IO.txt");
        fos.write(97);   // a
        fos.close();
    }
}
```



**2. 写出字节数组**：`write(byte[] b)`，每次可以写出数组中的数据

```java
public class IO_fos_02 {
    public static void main(String[] args) throws IOException {
        String path = System.getProperty("user.dir");
        FileOutputStream fos = new FileOutputStream(path + "/IO.txt");
        byte[] bytes = {97, 98, 99, 100, 101};
        fos.write(bytes);
        fos.close();
    }
}
```



## 字节输入流 InputStream

`java.io.InputStream `抽象类是表示字节输入流的所有类的超类，可以读取字节信息到内存中。它定义了字节输入流的基本共性功能方法。

- `public void close()` ：关闭此输入流并释放与此流相关联的任何系统资源。    
- `public abstract int read()`： 从输入流读取数据的下一个字节。 
- `public int read(byte[] b)`： 从输入流中读取一些字节数，并将它们存储到字节数组b中 。





##  FileInputStream类

`java.io.FileInputStream ` 类是文件输入流，从文件中读取字节。

### 构造方法

* `FileInputStream(File file)`： 通过打开与实际文件的连接来创建一个 FileInputStream ，该文件由文件系统中的 File对象 file命名。 
* `FileInputStream(String name)`： 通过打开与实际文件的连接来创建一个 FileInputStream ，该文件由文件系统中的路径名 name命名。  

当你创建一个流对象时，必须传入一个文件路径。该路径下，如果没有该文件,会抛出`FileNotFoundException` 。

### 读取字节数据

**1. 读取字节**

`read`，每次可以读取一个字节的数据，返回每次读取到个字节，提升为int类型，读取到文件末尾则，返回`-1`，

代码使用演示：

```java
  @Test
  public void test01() throws IOException {
      String path = System.getProperty("user.dir");
      FileInputStream fis = new FileInputStream(path + "/src/IO/Files/IO_02.txt");
      int len = 0;
      // 从输入流读取数据的下一个字节
      while ((len = fis.read()) != -1) {
          System.out.println((char) len);
      }
  }
```

`read` 方法会从文件中读取一个字节并返回，之后会将指针移动到下一个字节，一直读取到结束标记位置



---

**2. 使用字节数组读取**

`read(byte[] b)`，每次读取b的长度个字节到数组中，返回读取到的有效字节个数，读取末尾时返回`-1` 

```java
@Test
public void test02() throws IOException {
    String path = System.getProperty("user.dir");
    FileInputStream fis = new FileInputStream(path + "/src/IO/Files/IO_02.txt");
    byte[] bytes = new byte[3];
    int len = 0;
    while ((len = fis.read(bytes)) != -1) {
        System.out.println(new String(bytes));
    }
  fis.close();
}
```

创建一个长度为n，所有元素均为0的字节数组bytes作为缓冲区，`read`方法会从文件中读取n个字节保存在字节数组中，再次读取时，新读取的n个字节也会保存在字节数组中，会从索引0开始覆盖上一次保存的位置。

假设文件的内容是“abcdefg”，字节数组的大小为4，则需要读取两次，情况如下：

第一次：abcd

第二次：efgd

由于第二次只读取了三个字节，并没有覆盖数组中最后的“d”，所以会返回“efgd”

因此可以在打印字节数组时设置 `new String(bytes, 0, len)` ，这样只会输出本次读取个数的字节数





## 案例：文件复制



```java

```



## 读取中文字符

创建一个文本文件，其中还有两个中文字符，用一个大小为2的byte数组去读取会输出乱码

原因是在utf-8编码下中文字符占3个字节，如果一次读取两个字节，输出后的值并没有意义，所以会产生乱码。如果将byte数组的大小设置为3，则可以正确读取中文字符。

```java
@Test
public void test01() throws IOException {
    String path = System.getProperty("user.dir");
    FileInputStream fis = new FileInputStream(path + "/src/IO/Files/IO_03.txt");
    byte[] bytes = new byte[2];
    int len = 0;
    while ((len = fis.read(bytes)) != -1) {
        // 仅输出本次读取个数的字节数
        System.out.println(new String(bytes, 0, len));
    }
}

/*
输出乱码：
        �
        ��
        ��
*/
```





# 字符流🍭

当使用字节流读取文本文件时，如果遇到中文字符时，可能不会显示完整的字符，那是因为一个中文字符可能占用多个字节存储。所以Java提供一些字符流类，以字符为单位读写数据，专门用于处理文本文件。

## 字符输入流 Reader

`java.io.Reader`抽象类是表示用于读取字符流的所有类的超类，可以读取字符信息到内存中。它定义了字符输入流的基本共性功能方法。

- `public void close()` ：关闭此流并释放与此流相关联的任何系统资源。    
- `public int read()`： 从输入流读取一个字符。 
- `public int read(char[] cbuf)`： 从输入流中读取一些字符，并将它们存储到字符数组cbuf中。

## FileReader类  

`java.io.FileReader `类是读取字符文件的便利类。构造时使用系统默认的字符编码和默认字节缓冲区。

### 构造方法

- `FileReader(File file)`： 创建一个新的 FileReader ，给定要读取的File对象。   
- `FileReader(String fileName)`： 创建一个新的 FileReader ，给定要读取的文件的名称。  

当你创建一个流对象时，必须传入一个文件路径。类似于`FileInputStream`。

### 读取字符数据

1. **读取字符**：`read`方法，每次可以读取一个字符的数据，提升为int类型，读取到文件末尾，返回`-1`

   ```java
   public void test01() throws IOException {
       String path = System.getProperty("user.dir");
       FileReader fr = new FileReader(path + "/src/IO/Files/IO_03.txt");
       int len = 0;
       // fr.read() 返回读取到的字符
       while ((len = fr.read()) != -1) {
           System.out.print(len + " ");
           System.out.println((char)len);
       }
   }
   ```

   

2. **使用字符数组读取**：`read(char[] cbuf)`，每次读取b的长度个字符到数组中，返回读取到的有效字符个数，读取到末尾时，返回`-1` 

   ```java
   @Test
   public void test02() throws IOException {
       String path = System.getProperty("user.dir");
       FileReader fr = new FileReader(path + "/src/IO/Files/IO_03.txt");
       char[] chars = new char[3];
       int len = 0;
       // fr.read(chars) 返回读取到的字符个数，读取到的数据保存在chars中
       while ((len = fr.read(chars)) != -1) {
           System.out.println(new String(chars, 0, len));
       }
   }
   ```

   

## 字符输出流 Writer

`java.io.Writer `抽象类是表示用于写出字符流的所有类的超类，将指定的字符信息写出到目的地。它定义了字节输出流的基本共性功能方法。

- `void write(int c)` 写入单个字符。
- `void write(char[] cbuf) `写入字符数组。 
- `abstract  void write(char[] cbuf, int off, int len) `写入字符数组的某一部分,off数组的开始索引,len写的字符个数。 
- `void write(String str) `写入字符串。 
- `void write(String str, int off, int len)` 写入字符串的某一部分,off字符串的开始索引,len写的字符个数。
- `void flush() `刷新该流的缓冲。  
- `void close()` 关闭此流，但要先刷新它。 







## FileWriter类

`java.io.FileWriter `类是写出字符到文件的便利类。构造时使用系统默认的字符编码和默认字节缓冲区。

### 构造方法

- `FileWriter(File file)`： 创建一个新的 FileWriter，给定要读取的File对象。   
- `FileWriter(String fileName)`： 创建一个新的 FileWriter，给定要读取的文件的名称。  

当你创建一个流对象时，必须传入一个文件路径，类似于FileOutputStream。

### 写出字符数据

1. **写出字符**：`write(int b)` 方法，每次将一个字符数据写入缓冲区

   ```java
   @Test
   public void test01() throws IOException {
       String path = System.getProperty("user.dir");
       FileWriter fw = new FileWriter(path + "/src/IO/Files/IO_04.txt");
       fw.write(97);
       fw.write(98);
   }
   ```

   然鹅并没有输出任何字符，原因是FileWriter的 `write` 会将字符先写入缓冲区，等到缓冲区满的时候，再一次性写到文件里，缓冲区的默认大小是8kb，也就是8192个字节。

   可以调用FileWriter的 `flush` 方法主动输出缓冲区内的字符，另外，`close` 方法调用时也会刷新缓冲区。 

   ```java
   @Test
   public void test01() throws IOException {
       String path = System.getProperty("user.dir");
       FileWriter fw = new FileWriter(path + "/src/IO/Files/IO.txt");
       for (int i = 0; i < 1024; i++) {
           fw.write(97);
           fw.flush();
       }
       fw.close();
   }
   ```

   

   

2. **写出字符数组** ：`write(char[] cbuf)` 和 `write(char[] cbuf, int off, int len)` ，每次可以写出字符数组中的数据，用法类似FileOutputStream

   ```java
   @Test
   public void test03() throws IOException {
       String path = System.getProperty("user.dir");
       FileWriter fw = new FileWriter(path + "/src/IO/Files/IO.txt");
       char[] chars = {'a', 'b', 'c', 'd', 'e'};
       fw.write(chars, 0, 3);
       fw.close();
   }
   ```

   

3. **写出字符串**：`write(String str)` 和 `write(String str, int off, int len)` ，每次可以写出字符串中的数据，更为方便

   ```java
   @Test
   public void test04() throws IOException {
       String path = System.getProperty("user.dir");
       FileWriter fw = new FileWriter(path + "/src/IO/Files/IO.txt");
       String string = new String("接化发松果檀抖闪电鞭");
       fw.write(string, 0, 3);
       fw.close();
   }
   ```

   

4. **append方法写出数据**：`append(CharSequence csq)`返回的是Writer对象本身，所以可以链式调用

   ```java
   @Test
   public void test05() throws IOException {
       String path = System.getProperty("user.dir");
     	// 追加模式
       FileWriter fw = new FileWriter(path + "/src/IO/Files/IO.txt", true);
       String string = new String("接化发松果檀抖闪电鞭");
       fw.write(string);
       fw.close();
   }
   ```

   

### 关闭和刷新

因为内置缓冲区的原因，如果不关闭输出流，无法写出字符到文件中。但是关闭的流对象，是无法继续写出数据的。如果我们既想写出数据，又想继续使用流，就需要 `flush` 方法了。

* `flush` ：刷新缓冲区，流对象可以继续使用。
* `close `: 先刷新缓冲区，然后通知系统释放资源。流对象不可以再被使用了。





# IO异常处理🍭

## JDK7前的处理方法

```java
FileWriter fw = null;
try {
    fw = new FileWriter("fr.txt", true);
    for (int i = 0; i < 10; i++) {
        fw.write("hello " + i + "\n");
    }
} catch (IOException e) {
    e.printStackTrace();
} finally {
    if (fw != null) {
        try {
            fw.close();  // close方法抛出了IOException异常对象
        } catch (IOException e) {
            e.printStackTrace();
        }

    }
}
```

## JDK7的处理

还可以使用JDK7优化后的 `try-with-resource` 语句，该语句确保了每个资源在语句结束时关闭。所谓的资源（resource）是指在程序完成后，必须关闭的对象。

格式：

```java
try (创建流对象语句，如果多个,使用';'隔开) {
	// 读写数据
} catch (IOException e) {
	e.printStackTrace();
}
```

代码使用演示：

```java
public static void main(String[] args) {
    // 创建流对象
    try ( FileWriter fw = new FileWriter("fw.txt"); ) {
        // 写出数据
        fw.write("hello world"); //黑马程序员
    } catch (IOException e) {
        e.printStackTrace();
    }
}

```



# Properties🍭

## 概述

`java.util.Properties ` 继承于 ` Hashtable` ，来表示一个持久的属性集。它使用键值结构存储数据，每个键及其对应值都是一个字符串（不必再写泛型）。该类也被许多Java类使用，比如获取系统属性时，`System.getProperties` 方法就是返回一个`Properties`对象。

Properties集合是一个唯一和IO流相结合的集合

- 可以使用Properties集合中的方法store，把集合中的临时数据，持久化写入到硬盘中存储
- 可以使用Properties集合中的方法load，把硬盘中保存的文件（键值对），读取到集合中使用

## Properties类

Properties 类表示了一个持久的属性集。Properties可保存在流中或从流中加载。

### 构造方法

- `public Properties()` :创建一个空的属性列表。

### 基本的存储方法

- `public Object setProperty(String key, String value)` ： 保存一对属性，实际上调用了Hashtable的put方法  
- `public String getProperty(String key) ` ：使用此属性列表中指定的键搜索属性值，实际上调用了Map集合中的get方法
- `public Set<String> stringPropertyNames() ` ：返回所有键的名称的集合，实际上调用了Map集合中的`keySet`方法

```java
@Test
public void test01() {
    Properties properties = new Properties();
    properties.setProperty("月光", "S");
    properties.setProperty("强袭", "A");
    properties.setProperty("战车", "B");

    Set<String> set = properties.stringPropertyNames();
    for (String s : set) {
        System.out.println(s + " -> " + properties.getProperty(s));
    }
}
```



### 与流相关的方法

- `public void load(InputStream inStream)`： 从字节输入流中读取键值对。 

参数中使用了**字节输入流**，通过流对象，可以关联到某文件上，这样就能够加载文本中的数据了。

#### store方法

可以使用Properties集合中的方法store，把集合中的临时数据，持久化写入到硬盘中存储

- `void store(OutputStream out,String comments)`

- `void store(Writer writer,String comments)`

参数：

- `OutputStream out`：字节输出流，不能写入中文
- `Writer writer`：字符输出流，可以写中文
- `String comments`：注释，用来解释说明保存的文件是做什么用的

```java
@Test
public void test03() throws IOException {
    Properties properties = new Properties();
    properties.setProperty("月光", "S");
    properties.setProperty("强袭", "A");
    properties.setProperty("战车", "B");
    String path = System.getProperty("user.dir");
    FileWriter fw = new FileWriter(path + "/src/IO/Files/prop3.properties");
    // 从集合持久化到硬盘
    properties.store(fw, "wlp");
    fw.close();
}
```



#### load方法


可以使用Properties集合中的方法Load，把硬盘中保存的文件（键值对），读取到集合中使用

- `void Load(InputStream instream)`
- `void load(Reader reader)`

参数：

- `InputStream instream`：字节输入流，不能读取含有中文的键值对
- `Reader reader`：字符输入流，能读取含有中文的键值对

注意：

1. 存储键值对的文件中，键与值默认的连接符号可以使用 =，空格（其他符号）
2. 存储键值对的文件中，可以使用#进行注释，被注释的键值对不会再被读取
3. 存储键值对的文件中，键与值默认都是字符串，不用再加引号

```java
@Test
public void test05() throws IOException {
    Properties properties = new Properties();
    String path = System.getProperty("user.dir");
    FileReader fr = new FileReader(path + "/src/IO/Files/prop2.properties");
  	// 从硬盘读取到集合
    properties.load(fr);
    Set<String> set = properties.stringPropertyNames();
    for (String s : set) {
        System.out.println(s + " -> " + properties.getProperty(s));
    }
}
```



# 缓冲流🍭

##  概述

缓冲流,也叫高效流，是对4个基本的`FileXxx` 流的增强，所以也是4个流，按照数据类型分类：

* **字节缓冲流**：`BufferedInputStream`，`BufferedOutputStream` 
* **字符缓冲流**：`BufferedReader`，`BufferedWriter`

缓冲流的基本原理，是在创建流对象时，会创建一个内置的默认大小的缓冲区数组，通过缓冲区读写，减少系统IO次数，从而提高读写的效率。

## 字节缓冲流

### 构造方法

* `public BufferedInputStream(InputStream in)` ：创建一个新的缓冲输入流。 
* `public BufferedOutputStream(OutputStream out)`： 创建一个新的缓冲输出流。



---

**BufferedInputStream**

```java
@Test
public void test() throws IOException {
    // 字节缓冲输入流
    FileInputStream fis = new FileInputStream("src/IO/Files/buffer02.txt");
    BufferedInputStream bis = new BufferedInputStream(fis);
    byte[] bytes = new byte[1024];
    int len = 0;  // 记录每次读取到的有效字节个数
    while ((len = bis.read(bytes)) != -1) {
        System.out.println(new String(bytes, 0, len));
    }
    bis.close();
}
```

**BufferedOutputStream**

```java
@Test
public void test() throws IOException {
    // 字节缓冲输出流
    FileOutputStream fos = new FileOutputStream("src/IO/Files/buffer01.txt");
    BufferedOutputStream bos = new BufferedOutputStream(fos);
    bos.write("数据写入内部缓冲区".getBytes());
    bos.close();
}
```



## 字符缓冲流

### 构造方法

* `public BufferedReader(Reader in)` ：创建一个 新的缓冲输入流。 
* `public BufferedWriter(Writer out)`： 创建一个新的缓冲输出流。

构造举例，代码如下：

```java
// 创建字符缓冲输入流
BufferedReader br = new BufferedReader(new FileReader("br.txt"));
// 创建字符缓冲输出流
BufferedWriter bw = new BufferedWriter(new FileWriter("bw.txt"));
```

### 特有方法✨

字符缓冲流的基本方法与普通字符流调用方式一致，不再阐述，我们来看它们具备的特有方法。

* BufferedReader：`public String readLine()`: 读一行文字。 

  ```java
  @Test
  public void test() throws IOException {
      // readline方法
      BufferedReader br = new BufferedReader(new FileReader("buffer04.txt"));
      String line;
      while ((line = br.readLine()) != null) {
          System.out.println(line);
      }
      br.close();
  }
  ```

  

* BufferedWriter：`public void newLine()`: 写一行行分隔符,由系统属性定义符号。 

  ```java
  @Test
  public void test() throws IOException {
      // newLine方法
      BufferedReader br = new BufferedReader(new FileReader("buffer04.txt"));
      BufferedWriter bw = new BufferedWriter(new FileWriter("buffer05.txt"));
      String line;
      while ((line = br.readLine()) != null) {
          bw.write(line);
          bw.newLine();
      }
      bw.close();
      br.close();
  }
  ```

  







# 转换流🍭

`FileReader` 会查询IDE默认码表，将字节转换为字符（解码）

`FileWriter` 会查询IDE默认码表，将字符转换为字节（解码）

以上两者只能读取默认的码表

![](http://store.secretcamp.cn/uPic/xuliehua_1202105201854121621508052ogZVRXogZVRX.jpg)

## InputStreamReader类  

转换流`java.io.InputStreamReader`，是Reader的子类，是从字节流到字符流的桥梁。它读取字节，并使用指定的字符集将其解码为字符。它的字符集可以由名称指定，也可以接受平台的默认字符集。 

**构造方法**

- `OutputStreamWriter(OutputStream in)`: 创建一个使用默认字符集的字符流。 

- `OutputStreamWriter(OutputStream in, String charsetName)`: 创建一个指定字符集的字符流。

  ```java
  @Test
  public void test01() throws Exception {
      FileInputStream fis = new FileInputStream("src/IO/Files/ts01.txt");
      InputStreamReader isr = new InputStreamReader(fis, "utf-8");
      char[] chars = new char[1024];
      int len = 0;
      while ((len = isr.read(chars)) != -1) {
          System.out.println(new String(chars, 0, len));
      }
      isr.close();
  }
  ```

  



## OutputStreamWriter类

转换流`java.io.OutputStreamWriter` ，是Writer的子类，是从字符流到字节流的桥梁。使用指定的字符集将字符编码为字节。它的字符集可以由名称指定，也可以接受平台的默认字符集。 

**构造方法**

- `OutputStreamWriter(OutputStream in)`: 创建一个使用默认字符集的字符流。 
- `OutputStreamWriter(OutputStream in, String charsetName)`: 创建一个指定字符集的字符流。
  
  ```java
  @Test
  public void test02() throws Exception {
      FileOutputStream fos = new FileOutputStream("src/IO/Files/ts02.txt");
      OutputStreamWriter osw = new OutputStreamWriter(fos, "unicode");
      osw.write("年轻人不讲武德耗子尾汁");
      osw.close();
  }
  ```



# 序列化流🍭

## 概述

Java 提供了一种对象序列化的机制。用一个字节序列可以表示一个对象，该字节序列包含该对象的数据、对象的类型和对象中存储的属性等信息。字节序列写出到文件之后，相当于文件中持久保存了一个对象的信息。 

反之，该字节序列还可以从文件中读取回来，重构对象，对它进行反序列化。对象的数据、对象的类型和对象中存储的数据信息，都可以用来在内存中创建对象。看图理解序列化： 



## Serializable

要进行序列化和反序列化的类必须实现 `Serializable`接口，就会给类添加一个标记，当我们进行序列化和反序列化的时候，就会检测类上是否有这个标记，如果没有则会抛出 `NotSerializableException` 异常 。



`Serializable` 接口是一个标记型接口，即告诉 JVM 需要对该类进行序列化，如果没有自己声明一个`serialVersionUID` 变量，接口会默认生成一个 `serialVersionUID`

`serialVersionUID` 的详细的工作机制是：在序列化的时候系统将 `serialVersionUID` 写入到序列化的文件中去，当反序列化的时候系统会先去检测文件中的 `serialVersionUID` 是否跟当前的文件的 `serialVersionUID` 是否一致，如果一直则反序列化成功，否则就说明当前类跟序列化后的类发生了变化，比如是成员变量的数量或者是类型发生了变化，那么在反序列化时就会发生 `InvalidClassException` 异常。



## ObjectOutputStream类

`java.io.ObjectOutputStream ` 类，将Java对象的原始数据类型写出到文件,实现对象的持久存储。

### 构造方法

* `ObjectOutputStream(OutputStream out) `： 创建一个指定OutputStream的ObjectOutputStream

  ```java
  FileOutputStream fos = new FileOutputStream("src/IO/Files/se01.txt");
  ObjectOutputStream oos = new ObjectOutputStream(fos);
  ```



创建一个Student类

```java
class Student implements Serializable {
    private String name;
    private int age;
    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```



### 序列化实现

```java
@Test
public void test() throws Exception {
    FileOutputStream fos = new FileOutputStream("src/IO/Files/se01.txt");
    ObjectOutputStream oos = new ObjectOutputStream(fos);
    Student student = new Student("kiana", 18);
    oos.writeObject(student);
    oos.close();
```





## ObjectInputStream类

ObjectInputStream反序列化流，将之前使用ObjectOutputStream序列化的原始数据恢复为对象。 

### 构造方法

- `public ObjectInputStream(InputStream in) `： 创建一个指定InputStream的ObjectInputStream

  ```java
  FileInputStream fis = new FileInputStream("src/IO/Files/se01.txt");
  ObjectInputStream ois = new ObjectInputStream(fis);
  ```

  

### 反序列化操作

如果能找到一个对象的class文件，我们可以进行反序列化操作，调用`ObjectInputStream`读取对象的方法：

`public final Object readObject ()` : 读取一个对象。

```java
@Test
public void test() throws Exception {
    FileInputStream fis = new FileInputStream("src/IO/Files/se01.txt");
    ObjectInputStream ois = new ObjectInputStream(fis);
    Student student = (Student) ois.readObject();
    System.out.println(student);
}
```

对于 JVM 可以反序列化对象，它必须是能够找到class文件的类。如果找不到该类的class文件，则抛出一个 `ClassNotFoundException` 异常。  



## transient关键字

- static关键字：静态关键字

  - 静态优先于非静态加载到内存中（静态优先于对象进入到内存中）

  - 被static修饰的成员变量不能被序列化，能序列化的都是对象


- transient关键字：瞬态关键字
  - 被transient修饰的成员变量，不能被序列化
  - 没有static关键字的含义



## InvalidClassException

当JVM反序列化对象时，能找到class文件，但是class文件在序列化对象之后发生了修改，那么反序列化操作也会失败，抛出一个`InvalidClassException`异常。

也就是说，**没有对应的类就无法生成对应类的实例对象**。

`Serializable` 接口给需要序列化的类，提供了一个序列版本号。`serialVersionUID` 该版本号的目的在于验证序列化的对象和对应类是否版本匹配。







# 打印流🍭

## 概述

平时我们在控制台打印输出，是调用 `print` 方法和 `println` 方法完成的，这两个方法都来自于`java.io.PrintStream`类，该类能够方便地打印各种数据类型的值，是一种便捷的输出方式。



## PrintStream类

### 构造方法

* `public PrintStream(String fileName)  `： 使用指定的文件名创建一个新的打印流。

构造举例，代码如下：  

```java
PrintStream ps = new PrintStream("ps.txt")；
```

### 改变打印流向

`System.out`就是`PrintStream`类型的，只不过它的流向是系统规定的，打印在**控制台**上。对于流对象，我们可以改变它的流向。

```java
public class PrintDemo {
    public static void main(String[] args) throws IOException {
		// 调用系统的打印流,控制台直接输出97
        System.out.println(97);
      
		// 创建打印流,指定文件的名称
        PrintStream ps = new PrintStream("ps.txt");
      	
      	// 设置系统的打印流流向,输出到ps.txt
        System.setOut(ps);
      	// 调用系统的打印流,ps.txt中输出97
        System.out.println(97);
    }
}
```





# BIO🍭

## BIO的概念

传统 BIO 是一种同步阻塞的 I/O，I/O 在进行读写时，该线程将被阻塞，线程无法进行其它操作。

I/O 流在读取时，会阻塞，直到发生以下情况：

- 有数据可以读取
- 数据读取完成
- 发生异常



## 阻塞同步

服务端创建一个 ServerSocket， 然后客户端用一个 Socket 去连接服务端的 ServerSocket， ServerSocket 接收到了一个的连接请求就创建一个 Socket 和一个线程去跟客户端的 Socket 进行通讯。

客户端和服务端就进行阻塞式的通信，客户端发送一个请求，服务端 Socket 进行处理后返回响应，在响应返回前，客户端就阻塞等待。

客户端连接到服务端之前，服务端阻塞在 *accept* 函数，经过三次握手连接成功之后，服务端阻塞在 *read* 函数， 客户端阻塞在 *write* 函数。

![p77hp-duxvg](http://store.secretcamp.cn/uPic/p77hp-duxvg202108161550141629100214UkYWcpUkYWcp.gif)



将 *read* 函数的细节展开，会发现其阻塞在了两个阶段。

如果这个连接的客户端一直不发数据，那么服务端线程将会一直阻塞在 *read* 函数上不返回，也无法接受其他客户端连接。

<img src="http://store.secretcamp.cn/uPic/image-20210816160020761202108161600211629100821lumNF9lumNF9.png" alt="image-20210816160020761" style="zoom:50%;" />





## 多线程处理Socket

可以采取多线程的方式处理每个 Socket，每当有一个 Socket 连接，就分配一个线程去读写数据。

这样，当给一个客户端建立好连接后，服务端就可以立刻等待新的客户端连接，而不用阻塞在原客户端的 *read* 请求上。

不过，这并不是非阻塞，只不过用了多线程的手段使得主线程没有卡在 *read* 函数上。操作系统提供的 *read* 函数仍然是阻塞的。

![0h1k2-fewfa](http://store.secretcamp.cn/uPic/0h1k2-fewfa202108161606071629101167MnWMljMnWMlj.gif)



缺点：每次一个客户端接入，都需要在服务端创建一个线程来服务这个客户端，如果有大量客户端到来，就会造成服务端的线程数量成千上万，会造成服务端过载过高而崩溃。







## BIO的代码实现

BIO 的阻塞有两个地方：`accept()` 和 `read()`

本实现通过单线程处理单个连接

服务端程序：

```java
public class BioServerDemo {

    public static void main(String[] args) throws IOException {
        // 创建ServerSocket，并绑定端口
        ServerSocket serverSocket = new ServerSocket(9999);
        System.out.println("服务启动成功");
        while (true) {
            // 阻塞
            Socket socket = serverSocket.accept();
            System.out.println("连接成功");
            System.out.println("准备接收数据");
            byte[] bytes = new byte[1024];
            // 阻塞
            socket.getInputStream().read(bytes);
            System.out.println("接收到了数据：" + new String(bytes));
        }
    }

}
```



客户端程序：

```java
public class BioServerDemo {

    public static void main(String[] args) throws IOException {
        // 创建ServerSocket，并绑定端口
        ServerSocket serverSocket = new ServerSocket(9999);
        System.out.println("服务启动成功");
        while (true) {
            Socket socket = serverSocket.accept();
            System.out.println("连接成功");
            System.out.println("准备接收数据");
            byte[] bytes = new byte[1024];
            socket.getInputStream().read(bytes);
            System.out.println("接收到了数据：" + new String(bytes));
        }
    }

}
```





# NIO🍭

## NIO的概念

NIO（Non-blocking IO） 模型是一种同步非阻塞 I/O，主要有三大核心部分：

- Channel（通道）
- Buffer（缓冲区）
- Selector（多路复用器）

NIO 理解为 New-IO 比较好，因为 NIO 主要分为File I/O 和 Socket I/O 两大场景，而 “非阻塞” 是 Socket I/O 的特性。



## 非阻塞同步

传统 I/O 基于字节流和字符流进行操作，而 NIO 基于 Channel 和 Buffer 进行操作，数据总是从通道读取到缓冲区中，或者从缓冲区写入到通道中，其中 Selector 用于监听多个通道的事件，因为一个客户端并不是时时刻刻都有数据进行交互，服务端没必要开一个线程一直服务一个客户端。因此，通过 Selector 单个线程可以监听多个数据通道（I/O多路复用），相当于仅用一个线程轮询处理大量的客户端的请求，每次获取一批有事件的 Channel，然后对每个请求启动一个线程处理即可。

NIO 的核心是 “非阻塞同步”，一个 I/O 操作（read/write系统调用）其实分成了两个步骤：

1. 发起 I/O 请求
2. 实际的 I/O 读写。

阻塞 I/O 和非阻塞 I/O 的区别在于第一步，发起 I/O 请求的进程是否会被阻塞，在 NIO 中的 write 和 read 都不会被阻塞，所以 NIO 是非阻塞的。

同步 I/O 和异步 I/O 的区别就在于第二步，实际的 I/O 读写（内核态与用户态的数据拷贝）是否需要当前进程参与，如果需要进程参与则是同步 I/O ，如果不需要进程参与就是异步 I/O， NIO 中实际的读写需要当前进程参与，所以是同步的。

<img src="http://store.secretcamp.cn/uPic/image-20210824195014559202108241950141629805814WlQKJmWlQKJm.png" alt="image-20210824195014559" style="zoom:40%;" />



除了以上两个步骤，其他步骤不一定是非阻塞同步的，例如 Selector 调用 `select()` 函数是一个同步阻塞的操作，但是底层 epoll 的实现是异步的，当有客户端传来的数据到达 Socket 缓冲区时，经过 I/O 多路复用，服务端会使用一个线程去处理，但这并不是实时的，因为这条链路是事件驱动的，可以认为这个过程是异步的。

![image-20210428105848435](http://store.secretcamp.cn/uPic/image-20210428105848435202104281606421619597202ZLzBuHZLzBuH.png)





## BIO与NIO

- BIO 是面向流的，NIO 是面向缓冲区（块）的
  - I/O 面向流意味着每次从流中读一个或多个字节，直至读取所有字节，它们没有被缓存在任何地方。此外，它不能前后移动流中的数据。如果需要前后移动从流中读取的数据，需要先将它缓存到一个缓冲区。
  - NIO 的缓冲导向方法略有不同，数据读取到一个它稍后处理的缓冲区，需要时可在缓冲区中前后移动。这就增加了处理过程中的灵活性。

- BIO 是阻塞的，NIO 是非阻塞的
  - BIO 中当一个线程调用 `read()` 或 `write()` 时，该线程被阻塞，直到有一些数据被读取，或数据完全写入，该线程在此期间不能再干任何事情了。
  -  NIO 的非阻塞模式，使一个线程从某通道发送请求读取数据，但是它仅能得到目前可用的数据，如果目前没有数据可用时，就什么都不会获取。而不是保持线程阻塞，所以直至数据变得可以读取之前，该线程可以继续做其他的事情。非阻塞写也是如此，一个线程请求写入一些数据到某通道，但不需要等待它完全写入，这个线程同时可以去做别的事情。线程通常将非阻塞 I/O 的空闲时间用于在其它通道上执行 I/O 操作，所以一个单独的线程现在可以管理多个输入和输出 Channel 。



## Channel

Channel，一般是指 “通道” ，通道表示打开到 I/O 设备（文件、套接字）的连接

Channel 和 I/O 中的流类似，但也有区别：

1. Channel 是双向的，既可以读又可以写，而流是单向的
2. Channel 可以进行异步的读写
3. Channel 的读写必须通过 Buffer 对象

Channel 的主要实现有：

- `FileChannel` （文件）
- `DatagramChannel` （UDP）
- `SocketChannel` （TCP Client）
- `ServerSocketChannel`（TCP Server）



<img src="http://store.secretcamp.cn/uPic/image-20210428155500237202104281555021619596502taZq1ytaZq1y.png" alt="image-20210428155500237" style="zoom:40%;" />





### 文件读写

通过 FileChannel 实现文件拷贝

```java
public static void copyFileUseNIO(String src, String dst) throws IOException {
    // 声明源文件和目标文件
    FileInputStream fi = new FileInputStream(new File(src));
    FileOutputStream fo = new FileOutputStream(new File(dst));
    // 获得传输通道channel
    FileChannel inChannel = fi.getChannel();
    FileChannel outChannel = fo.getChannel();
    // 获得容器buffer
    ByteBuffer buffer = ByteBuffer.allocate(4);
    while (true) {
        // 判断是否读完文件
        int eof = inChannel.read(buffer);
        if (eof == -1) {
            break;
        }
        // 重设一下buffer的position=0，limit=position
        buffer.flip();
        // 开始写
        outChannel.write(buffer);
        // 写完要重置buffer，重设position=0,limit=capacity
        buffer.clear();
    }
    inChannel.close();
    outChannel.close();
    fi.close();
    fo.close();
}
```



### 网络通信

如果基于 NIO 进行网络通信，采取的就是多路复用的 I/O 模型，这个多路复用 I/O 模型针对的是网络通信中的 I/O 场景而言的。

在基于 Socket 进行网络通信的时候，如果有多个客户端与服务端建立了 Socket 连接，那么就需要维护多个 Socket 连接。对于 I/O多路复用模型，在 Java 代码的层面进行一个 select 函数调用，然后会进入一个同步等待的状态，必须在这里同步等待某个 Socket 连接有请求到来。

在 select 函数的底层实现中，会通过非阻塞的方式轮询各个Socket，任何一个Socket 如果没有数据到达，那么根据非阻塞的特性会立即返回一个信息，然后 select 函数可以轮询下一个 Socket，不会阻塞在某个 Socket 上。

<img src="http://store.secretcamp.cn/uPic/image-20210816101931465202108161019311629080371W4yGl6W4yGl6.png" alt="image-20210816101931465" style="zoom:50%;" />







## Buffer

Buffer 指 “缓冲区”，实际上是一个容器，一个连续数组。

Channel 提供从文件、网络读取数据的渠道，但是读写的数据都必须经过 Buffer 。

![image-20210816104842243](http://store.secretcamp.cn/uPic/image-20210816104842243202108161048421629082122R3hiA0R3hiA0.png)



NIO 中的关键 Buffer 实现有：

- `ByteBuffer`

- `CharBuffer`
- `DoubleBuffer`
- `FloatBuffer`
- `IntBuffer`
- `LongBuffer`
- `ShortBuffer`

以上对应基本数据类型，此外还有

- `MappedByteBuffer`
- `HeapByteBuffer`
- `DirectByteBuffer`



### 缓冲区的类型

Buffer 主要分为两种类型：

- 直接缓冲区：通过 `allocate()` 方法分配的缓冲区，将缓冲区建立在 JVM 的内存中

- 非直接缓冲区：通过 `allocateDirect()` 方法分配的缓冲区，将缓冲区建立在操作系统的物理内存中，可以利用零拷贝提高效率



对于非直接缓冲区，使用的是传统 I/O 的数据拷贝流程

<img src="http://store.secretcamp.cn/uPic/image-20210816124713353202108161247131629089233lVJyealVJyea.png" alt="image-20210816124713353" style="zoom:40%;" />



对于直接缓冲区，则使用零拷贝进行优化

<img src="http://store.secretcamp.cn/uPic/image-20210816124811365202108161248111629089291Ut8yS7Ut8yS7.png" alt="image-20210816124811365" style="zoom:45%;" />



### Buffer的使用

Buffer 的使用遵循以下几个步骤：

- 分配空间
- 写入数据到 Buffer
- 调用 `filp()` 方法
- 从Buffer中读取数据
- 调用 `clear()` 方法或者 `compact()` 方法





#### 状态变量

控制 Buffer 状态的三个变量：

- capacity：代表缓冲区的最大容量，一般新建一个缓冲区的时候，limit 的值和 capacity 的值默认是相等的。

- position：跟踪已经写了多少数据或读了多少数据，它指向的是下一个要操作的数据元素的位置

- limit：代表还有多少数据可以取出或还有多少空间可以写入，它的值小于等于capacity

- mark：用于记录当前 position 的前一个位置或者默认是 -1
  



#### flip

 `flip()` 方法把当前的指针位置 position 设置成 limit ，再将当前指针 position 指向数据的最开始端，它的作用相当于从写模式切换到读模式。



调用 `flip()` 方法之前：

<img src="http://store.secretcamp.cn/uPic/image-20210816105152267202108161051521629082312KbvQNFKbvQNF.png" alt="image-20210816105152267" style="zoom:50%;" />



调用 `flip()` 方法之后：

<img src="http://store.secretcamp.cn/uPic/image-20210816105203436202108161052031629082323cveNGJcveNGJ.png" alt="image-20210816105203436" style="zoom:50%;" />



#### clear

`clear()`方法将 position 将被设回0，limit 设置成 capacity，换句话说，可以认为 Buffer 被清空了。



#### compact

`compact()` 方法将所有未读的数据拷贝到 Buffer 起始处，然后将 position 设到最后一个未读元素正后面，limit 设置成 capacity 。



### MappedByteBuffer

Channel 就相当于操作系统中的内核缓冲区，有可能是读缓冲区，也有可能是网络缓冲区，而 Buffer 就相当于操作系统中的用户缓冲区。

`MappedByteBuffer` 的底层就是通过 mmap 实现的。

`MappedByteBuffer` 是一个抽象类，`DirectByteBuffer` 是它的一个具体实现。



### DirectByteBuffer

`DirectByteBuffer` 是堆外内存的代理类







## Selector

NIO 之所以叫做非阻塞 I/O，主要是因为 NIO 在网络通信中的非阻塞特性被广泛使用。

Selector 是一个多路复用器，它运行单线程管理多个被注册到其之上的 Channel 。

Selector 的实现取决于操作系统，目前 IO 多路复用实现主要包括四种：select、poll、epoll、kqueue



Selector 对应操作系统的 Epoll 描述符，将获取到的 Socket 连接的文件描述符的事件绑定到 Selector 对应的 Epoll 文件描述符上，进行事件的异步通知。



## NIO中的零拷贝

BIO 下，调用 `write()` 方法将 byte 数组从 JVM 拷贝到内核缓冲区，然后再拷贝到 Socket 缓冲区

如果使用 `MappedByteBuffer` ，JVM 在网络传输中允许使用堆外内存，JVM 直接将 buffer 分配在堆外内存，然后调用 `write()` 方法，此时操作系统会直接将数据从内核缓冲区拷贝到 Socket 缓冲区中，减少了一次拷贝过程，实现了零拷贝。

之所以要使用堆外内存，一是因为 buffer 数据地址是连续的且属于大对象，如果在 JVM 中可能会收到 GC 影响，对象可能会被清理、移动（晋升到老年代），二是零拷贝技术能够提高传输效率。



代码分析：

在 `SocketChannelImpl` 中的 `write()` 方法调用了 `IOUtil.write()` 方法

```java
static int write(FileDescriptor var0, ByteBuffer var1, long var2, NativeDispatcher var4) throws IOException {		
  	// 如果是堆外内存，直接拷贝到Socket缓冲区
    if (var1 instanceof DirectBuffer) {
        return writeFromNativeBuffer(var0, var1, var2, var4);
    } 
    // 如果不是堆内内存，先拷贝到堆外，再拷贝到Socket缓冲区
    else {
        int var5 = var1.position();
        int var6 = var1.limit();

        assert var5 <= var6;

        int var7 = var5 <= var6 ? var6 - var5 : 0;
        // 申请堆外空间并拷贝
        ByteBuffer var8 = Util.getTemporaryDirectBuffer(var7);

        int var10;
        try {
            var8.put(var1);
            var8.flip();
            var1.position(var5);
            // 拷贝到Socket缓冲区
            int var9 = writeFromNativeBuffer(var0, var8, var2, var4);
            if (var9 > 0) {
                var1.position(var5 + var9);
            }

            var10 = var9;
        } finally {
            Util.offerFirstTemporaryDirectBuffer(var8);
        }

        return var10;
    }
}
```



所以，如果直接使用 `DirectByteBuffer` ，就可以利用零拷贝，否则还要多拷贝一次。



## 堆外内存的释放

在堆外内存的释放中，`DirectByteBuffer` 中有一个 cleaner 对象。

clean 对象继承了虚引用，所以它持有 DirectByteBuffer 对象， 同时持有一个堆外内存释放器 `Deallocator`

```java
cleaner = Cleaner.create(this, new Deallocator(base, size, cap));
```



当 `DirectByteBuffer` 对象失去强引用后会被判定为垃圾，cleaner 作为仅被 DirectByteBuffer 对象引用的对象，也会被判定位垃圾，但 cleaner 是一个特殊的对象，因为当它被 GC 之后先被转移到在 pending 队列，然后不会像其他 `Reference` 一样转移到 `RefenceQueue`  ，而是直接执行 `cleaner.clean()` 方法，直接调用 `Deallocator`  的方法释放堆外内存。

```java
public void clean() {
    if (remove(this)) {
        try {
            // 关键在这里
            this.thunk.run();
        } catch (final Throwable var2) {
            AccessController.doPrivileged(new PrivilegedAction<Void>() {
                public Void run() {
                    if (System.err != null) {
                        (new Error("Cleaner terminated abnormally", var2)).printStackTrace();
                    }

                    System.exit(1);
                    return null;
                }
            });
        }
    }
}
```





## 非阻塞同步的代码实现

本实现通过单个线程来处理多个连接

在这个实现下，`accept()` 不再阻塞，返回一个非负数作为 socket 的文件描述符，返回 -1 表示失败。通过这个文件描述符，Java 可以调用 native 方法通过 socket 进行数据传输。

当前线程将所有 socket 的文件描述符保存在 channelList 中，使用轮询的方式遍历这些文件描述符，实现数据通信和处理新连接。

这样可以节约大量的线程资源，但是当前线程需要不断地轮询去检查是否有 socket 就绪，每次都要调用 `read()` 方法，涉及到用户态内核态的转换，如果建立的连接数太多，就会造成大量的无效遍历，占用很多 CPU 时间。



```java
public class NioServerDemo {

    // 保存客户端连接
    static List<SocketChannel> channelList = new ArrayList<>();

    public static void main(String[] args) throws IOException, InterruptedException {

        // 创建NIO ServerSocketChannel
        ServerSocketChannel serverSocket = ServerSocketChannel.open();
        serverSocket.bind(new InetSocketAddress(9998));

        // 设置ServerSocketChannel为非阻塞
        serverSocket.configureBlocking(false);
        System.out.println("服务启动成功");

        while (true) {
            SocketChannel socketChannel = serverSocket.accept();
            if (socketChannel != null) { // 如果有客户端进行连接
                System.out.println("连接成功");
                // 设置SocketChannel为非阻塞
                socketChannel.configureBlocking(false);
                // 保存客户端连接在List中
                channelList.add(socketChannel);
            }

            // 遍历连接进行数据读取
            Iterator<SocketChannel> iterator = channelList.iterator();
            while (iterator.hasNext()) {
                SocketChannel channel = iterator.next();
                ByteBuffer byteBuffer = ByteBuffer.allocate(128);
                int read = channel.read(byteBuffer);
                // 如果有数据，把数据打印出来
                if (read > 0) {
                    System.out.println("接收到消息：" + new String(byteBuffer.array()));
                } else if (read == -1) { // 如果客户端断开，把socket从集合中去掉
                    iterator.remove();
                    System.out.println("客户端断开连接");
                }
            }
        }
    }
}
```





# AIO🍭

## AIO的概念

Java 7 中引入了 NIO 的改进版 AIO（Asynchronous I/O），它是异步 I/O 模型。

异步 I/O 是基于事件和回调机制实现的，也就是应用操作之后会直接返回，不会堵塞在那里，当后台处理完成，操作系统会通知相应的线程进行后续的操作。

目前来说 AIO 的应用还不是很广泛。Netty 之前也尝试使用过 AIO，不过又放弃了。这是因为，Netty 使用了 AIO 之后，在 Linux 系统上的性能并没有多少提升。
