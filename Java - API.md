# 包装类☕

## 概念

Java是面向对象的语言，但并不是 “纯面向对象”，Java中的基本数据类型都不是对象，在实际使用中需要将基本数据类型转变为对象，便于操作，也就是 “包装类” ，包装类均位于 `java.lang` 包中。

| 基本数据类型 | 包装类    |
| ------------ | --------- |
| byte         | Byte      |
| boolean      | Boolean   |
| short        | Short     |
| char         | Character |
| int          | Integer   |
| long         | Long      |
| float        | Float     |
| double       | Double    |

**包装类的作用**

- 提供字符串、基本类型数据、对象之间互相转化的方式
- 包含每种基本数据类型的相关属性，如最大值、最小值等



## 装箱与拆箱

* **装箱**：从基本类型转换为对应的包装类对象。

* **拆箱**：从包装类对象转换为对应的基本类型。

**Integer类的基本操作**

```java
// 基本数据类型转为包装类对象（装箱）
Integer a = new Integer(3);      // 构造方法
Integer b = Integer.valueOf(5);	 // 静态方法 该方法会优先从缓存中返回对象

// 包装类对象转为基本数据类型（拆箱）
int c = b.intValue();	         // 成员方法
double d = b.doubleValue();		 // 成员方法

// 字符串转为包装类对象（装箱）
Integer e = new Integer("123");       // 构造方法
Integer f = Integer.parseInt("123");  // 静态方法 

// 包装类对象转为字符串（拆箱）
String s = f.toString();  // 成员方法

// 常量
System.out.println(Integer.MAX_VALUE);
System.out.println(Integer.MIN_VALUE);
```



## 自动装箱与拆箱

基本类型与对应的包装类对象之间，来回转换的过程称为 “装箱” 与 “拆箱”

### 自动装箱

自动装箱指将基本类型就自动地封装到与它相同类型的包装中，简化了语法。

```java
Integer i = 10;
```

本质上是编译器在编译时为我们添加了

```java
Integer i = Integer.valueOf(10);
```

类似的有

```java
i = i + 5;  // i = i.intValue() + 5;
```



### 自动拆箱

```java
int a = new Integer(10);
```

本质上是编译器在编译时为我们添加了

```java
int a = new Integer(10).intValue();
```

## 基本类型与字符串之间的转换

### 基本类型转换为String

- 基本数据类型与 `""` 之间相连
- 包装类中重载的静态方法： ``static String toString(int i)`
- `String` 类中重载的静态方法：`static String valueOf(int i)`

### String转换成对应的基本类型

除了 `Character` 类之外，其他所有包装类都具有静态方法可以将字符串参数转换为对应的基本类型：

- `public static byte parseByte(String s)`：将字符串参数转换为对应的byte基本类型。
- `public static short parseShort(String s)`：将字符串参数转换为对应的short基本类型。
- `public static int parseInt(String s)`：将字符串参数转换为对应的int基本类型。
- `public static long parseLong(String s)`：将字符串参数转换为对应的long基本类型。
- `public static float parseFloat(String s)`：将字符串参数转换为对应的float基本类型。
- `public static double parseDouble(String s)`：将字符串参数转换为对应的double基本类型。
- `public static boolean parseBoolean(String s)`：将字符串参数转换为对应的boolean基本类型。



## 包装类的缓存

缓存实际上是一个Integer数组，系统初始的时候创建了一个范围为 `[-128, 127]` 的缓存数组，如果创建的对象在这个范围之内，则直接获取缓存中的对象；不在缓存范围之类则创建新的Integer对象。

```java
Integer int1 = Integer.valueOf(1024);
Integer int2 = Integer.valueOf(1024);
System.out.println(int1 == int2);  		// false
System.out.println(int1.equals(int2));	// true
```



```java
Integer int3 = Integer.valueOf(100);
Integer int4 = Integer.valueOf(100);
System.out.println(int3 == int4);       // true
System.out.println(int3.equals(int4));  // true
```



`new Integer` 会创建新的对象，不会从缓存中返回对象

```java
Integer int1 = Integer.valueOf(100);
Integer int2 = Integer.valueOf(100);
Integer int3 = new Integer(100);
System.out.println(int1 == int2);  // true
System.out.println(int1 == int3);  // false
```



## 缓存分析

new Integer(100) 与 Integer.valueOf(100) 的区别在于：

- new Integer(100) 每次都会新建一个对象；
- Integer.valueOf(100) 会使用缓存池中的对象，多次调用会取得同一个对象的引用

`valueOf()` 先判断值是否在缓存池中，如果在的话就直接返回缓存池的内容，编译器也会在自动装箱过程调用 `valueOf()` 方法。

```java
public static Integer valueOf(int i) {
    if (i >= IntegerCache.low && i <= IntegerCache.high)  // -128 ~ 127
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}
```



# Object☕

`java.lang.Object `是Java语言中的根类，即所有类的父类。它中描述的所有方法子类都可以使用。在对象实例化的时候，最终找的父类就是 `Object` 。

如果一个类没有特别指定父类，那么默认则继承自 `Object` 类。



## 方法

Object 类当中包含的方法有 11 个



### hashcode

`Object#hashcode` 方法返回的 hash 值即该对象的 32 位逻辑地址

```java
int[] array = new int[]{1, 2, 3};
System.out.println(array.hashCode());  // 78308db1
```



### toString

`public String toString()`：返回该对象的字符串表示。

```java
public String toString() {
    return getClass().getName() + "@" + Integer.toHexString(hashCode());
}
```

Object类中的 `toString` 方法会返回对象的 `类型@十六进制地址`



### equals

`public boolean equals(Object obj)`：指示其他某个对象是否与此对象“相等”。

```java
public boolean equals(Object obj) {
    return (this == obj);
}
```

如果没有覆盖重写equals方法，那么Object类中默认进行 `==` 运算符的对象地址比较，只要不是同一个对象，结果必然为 false。

如果希望进行对象内容比较，即所有或指定的部分成员变量相同就判定相同，则可以覆盖重写equals方法。











## equals和==

`==`  ：对于基本数据类型，是比较双方的值，对于引用数据数据类型，比较是否是同一个对象

`equals` ：是比较双方的内容是否相等。





## 重写equals方法

对于 User 对象，只需姓名和年龄相等则认为是同一个对象。

如果不重写 `equals` 方法，那么 比较的就是两个对象的地址，肯定是不相等的

```java
@Override
public boolean equals(Object obj) {  
  	//  地址相等
    if(this == obj){
        return true;
    }
		// 非空性：对于任意非空引用x，x.equals(null)应该返回false。
    if(obj == null){
        return false;
    }

    if(obj instanceof User){
        User other = (User) obj;
        // 需要比较的字段相等，则这两个对象相等
        if(Objects.equals(this.name, other.name) && Objects.equals(this.age, other.age)){
            return true;
        }
    }

    return false;
}
```



```java
// 重写equals方法
@Override
public boolean equals(Object obj) {
    // hashCode相等 => 相等
    if (this == obj) return true;
    // 不是同一个类 => 不相等
    if (obj == null || getClass() != obj.getClass()) return false;
    Player player = (Player) obj;
  	// 比较自定义的属性是否相等
    return id == player.id;
}
```



## 重写hashCode方法

如果重写 `equals` 方法，则还必须要重写 `hashCode` 方法

如果不重写 `hashCode` 方法，会出现什么样的问题？

- equals效率低

  重写 `hashCode` 方法，先进行哈希判断，如果相同那肯定是同一个对象，那么就没必要再进行 `equals` 的比较了，这样就大大减少了 equals 比较的次数。

- 影响哈希容器数据一致性

  用 `HashMap` 和 `HashSet` 之类的基于哈希算法的集合类存储自定义对象类中，会根据对象的哈希值去进行寻址，如果不重写 `hashCode` 方法，则默认使用 `Object` 的 `hashCode()` 方法，而这个方法返回的是对象的内存地址，这就会把相同的对象当成不同的对象，因为它们的哈希值是不同的。

如果重写了 `equals` 而未重写 `hashcode` 方法，可能就会出现两个内容相同的对象  `hashcode` 不相同的，导致这两个对象都能被放入 HashSet 中。

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
    int id;
    String name;
    int age;

    @Override
    public boolean equals(Object obj) {
        if (this == obj) {
            return true;
        }
        if (obj == null) {
            return false;
        }
        if (obj instanceof User) {
            User other = (User) obj;
            if (Objects.equals(this.name, other.name) && Objects.equals(this.age, other.age)) {
                return true;
            }
        }
        return false;
    }
  
    public static void main(String[] args) {
        User user1 = new User(1, "老哥", 18); 
        User user2 = new User(1, "老哥", 18);
        System.out.println(user1.hashCode()); // 666988784
        System.out.println(user2.hashCode()); // 1414644648
        Set<User> set = new HashSet<>();
        System.out.println(set.add(user1)); // true
        System.out.println(set.add(user2)); // true
    }  
}
```

会发现p1和p2都被放入了 `HashSet` 之中，因为没有重写 `hashCode` 方法，这两个对象的哈希值是不同的。







## Objects工具类

JDK7 添加了一个 `Objects` 工具类，它提供了一些方法来操作对象，它由一些静态的实用方法组成，这些方法是null-save（空指针安全的）或 null-tolerant（容忍空指针的）

Object的equals方法容易抛出空指针异常，而Objects类中的equals方法就优化了这个问题。

- `public static boolean equals(Object a, Object b)`：判断两个对象是否相等。

  ```java
  public static boolean equals(Object a, Object b) {  
      return (a == b) || (a != null && a.equals(b));  
  }
  ```

  



# String☕

## 概述

`java.lang.String` 类代表字符串。Java程序中所有的字符串文字（例如 "abc" ）都可以被看作是实现此类的实例，String类又称为不可变字符序列。

String类实际上是char数组，`final` 关键字使String类只能在初始化时进行一次赋值

```java
private final char value[];
```

`"abc"` 等效于 `char[] data={ 'a' , 'b' , 'c' }` 。



## 字符串是不可变对象

给一个 `String` 重新赋值 ，不是在原内存地址上修改数据，而是在堆中生成一个新对象，指向一个新对象的地址。

<img src="http://store.secretcamp.cn/uPic/image-20210314090606400202103140906061615683966gbzboxgbzbox.png" alt="image-20210314090606400" style="zoom: 50%;" />

`String` 被设计为不可变是因为

- 在多线程环境下，`String` 是不可变对象不能被写入，所以线程安全。

- `String` 经常作为 `HashMap` 和 `HashSet` 的元素，如果 `String` 是可变对象，被修改后就会破坏hash一致性。
- 为了节省内存空间提供效率，`String` 是缓存在字符串常量池中的，不可变是一个基本的保证。



## 构造方法

- `public String()` ：初始化新创建的 String对象，以使其表示空字符序列。

- `public String(char[] value)` ：通过当前参数中的字符数组来构造新的String。

- `public String(byte[] bytes)` ：通过使用平台的默认字符集解码当前参数中的字节数组来构造新的String。

```java
// 字面量生成
String str = "abc";

// 无参构造
String str = new String("abc");

// 通过字符数组构造
char[] chars = {'a', 'b', 'c'};
String str = new String(chars);
    
// 通过字节数组构造
byte bytes[] = {97, 98, 99};
String str = new String(bytes);
```





```java
new String("abc");
```

使用这种方式一共会创建两个字符串对象（前提是 String Pool 中还没有 "abc" 字符串对象）。

- "abc" 属于字符串字面量，因此编译时期会在 String Pool 中创建一个字符串对象，指向这个 "abc" 字符串字面量；
- 而使用 new 的方式会在堆中创建一个字符串对象。



## 常用方法

定义测试字符串

```java
String s1 = "core JAVA";
String s2 = "core JAVA";
String s3 = new String("core JAVA");
String s4 = "   hello  ";
```

- `public boolean equals (Object anObject)` ：将此字符串与指定对象进行比较。

  ```java
  public boolean equals(Object anObject) {
    // 如果是同一个对象，直接返回true
      if (this == anObject) {
          return true;
      }
    // 如果不是同一个对象，但该对象是String的实例
      if (anObject instanceof String) {
          String anotherString = (String)anObject;
          int n = value.length;
          if (n == anotherString.value.length) {
              char v1[] = value;
              char v2[] = anotherString.value;
              int i = 0;
            // 逐个遍历char数组，比较每个字符
              while (n-- != 0) {
                  if (v1[i] != v2[i])
                      return false;
                  i++;
              }
              return true;
          }
      }
      return false;
  }
  ```

  

  ```java
  s1 == s2;       // true    
  s1 == s3;       // false
  s1.equals(s2);  // true
  s1.equals(s3);  // true
  ```

- `public int length ()` ：返回此字符串的长度

    ```java
    s1.length()  // 9
    ```

- `public char charAt (int index)` ：返回指定索引处的 char值。

    ```java
    s1.charAt(2);  // 返回指定索引出的值  (0, length-1)
    ```

- `public int indexOf (String str)` ：返回指定子字符串第一次出现在该字符串内的索引。

  ```java
  s1.indexOf("JAVA");  // 5
  ```

- `public String substring (int beginIndex, int endIndex)` ：返回一个子字符串，从beginIndex到endIndex截取字符串。含beginIndex，不含endIndex。

  ```java
  s1.substring(0,4)  // core
  ```

- `public String[] split(String regex)` ：将此字符串按照给定的regex（规则）拆分为字符串数组。

  ```java
  String[] arr = s1.split(" ");
  ```

- `public char[] toCharArray ()` ：将此字符串转换为新的字符数组。

  ```java
  char[] chars = s1.toCharArray();
  ```

- `public byte[] getBytes ()` ：使用平台的默认字符集将该 String编码转换为新的字节数组。

  ```java
  byte[] bytes = s1.getBytes();
  ```

- `public String replace (CharSequence target, CharSequence replacement)` ：将与target匹配的字符串使用replacement字符串替换。

  ```java
  String replace = str.replace("JAVA", "Python");  // core Python
  ```

- `public String trim()`：去除首尾的空格

  ```java
  s4.trim();  // hello
  ```

- `public String toUpperCase()`：转大写

  ```java
  s1.toUpperCase()  // CORE JAVA
  ```

- `public boolean startsWith(String prefix)`：是否以某字符开头

  ```java
  s1.startsWith("J")  // true
  ```









## 字符串常量池

字符串常量池（String Pool）保存着所有字符串字面量（literal strings），这些字面量在编译时期就确定。不仅如此，还可以使用 String 的 intern() 方法在运行过程将字符串添加到 String Pool 中。

当一个字符串调用 `intern()` 方法时，如果 String Pool 中已经存在一个字符串和该字符串值相等（使用 equals() 方法进行确定），那么就会返回 String Pool 中字符串的引用；否则，就会在 String Pool 中添加一个新的字符串，并返回这个新字符串的引用。

下面示例中，s1 和 s2 采用 new String() 的方式新建了两个不同字符串，而 s3 和 s4 是通过 `s1.intern()` 方法取得同一个字符串引用。`intern()` 首先把 s1 引用的字符串放到 String Pool 中，然后返回这个字符串引用。因此 s3 和 s4 引用的是同一个字符串。

```java
String s1 = new String("aaa");
String s2 = new String("aaa");
System.out.println(s1 == s2);           // false
String s3 = s1.intern();
String s4 = s1.intern();
System.out.println(s3 == s4);           // true
```

如果是采用 "abc" 这种字面量的形式创建字符串，会自动地将字符串放入 String Pool 中。

```java
String s5 = "abc";
String s6 = "abc";
System.out.println(s5 == s6);  // true
```







## 常量池案例

```java
String s1 = "Hello";
String s2 = "Hello";
String s3 = "Hel" + "lo";
String s4 = "Hel" + new String("lo");
String s5 = new String("Hello");
String s6 = s5.intern();
String s7 = "H";
String s8 = "ello";
String s9 = s7 + s8;
          
System.out.println(s1 == s2);  // true
System.out.println(s1 == s3);  // true
System.out.println(s1 == s4);  // false
System.out.println(s4 == s5);  // false
System.out.println(s1 == s6);  // true
System.out.println(s1 == s9);  // false


```



### s1 == s2

s1、s2在赋值时，均使用的字符串字面量，在编译期间，这种字面量会直接放入字节码文件的常量池中，从而实现复用，载入运行时常量池后，s1、s2指向的是同一个内存地址，所以相等。

### s1 == s3

s3虽然是动态拼接出来的字符串，但是所有参与拼接的部分都是已知的字面量，在编译期间，这种拼接会被编译器优化，因此 `String s3 = "Hel" + "lo"` 在字节码文件中被优化成 `String s3 = "Hello"` 

### s1 == s4

s4虽然也是拼接出来的，但 `new String("lo")` 这部分不是已知字面量，是一个不可推测的部分，编译器不会优化，必须等到运行时才可以确定结果，所以地址肯定不同。

对于所有包含new方式新建对象（包括null）的“+”连接表达式，它所产生的新对象都不会被加入字符串池中。

<img src="http://store.secretcamp.cn/uPic/image-20210123193730959202101231937311611401851aaLnKEaaLnKE.png" alt="image-20210123193730959" style="zoom: 50%;" />

### s1 == s5

两者都在堆中，但地址不同

### s1 == s6

`intern` 方法会尝试将 Hello 字符串添加到常量池中，并返回其在常量池中的地址，因为常量池中已经有了 Hello 字符串，所以`intern` 方法直接返回地址。

### s1 == s9

虽然s7、s8在赋值的时候使用的字符串字面量，但是拼接成s9的时候，s7、s8作为两个变量，都是不可推测的，不能在编译期被确定，所以不做优化，只能等到运行时，在堆中创建s7、s8拼接成的新字符串，在堆中地址不确定，不可能与方法区常量池中的s1地址相同。

<img src="http://store.secretcamp.cn/uPic/image-202101231949047202021012319490416114025444B5cR74B5cR7.png" alt="image-20210123194904720" style="zoom: 43%;" />



### 特殊情况case1

```java
public static final String A = "ab"; // 常量A
public static final String B = "cd"; // 常量B
public static void main(String[] args) {
     String s = A + B;   
     String t = "abcd";   
    if (s == t) {   
         System.out.println("true");   
     } else {   
         System.out.println("false");   
     }   
} 

// true
```

A和B都是常量，值是固定的，因此s的值也是固定的，它在类被编译时就已经确定了。也就是说：`String s = A + B` 等同于：`String s = "ab" + "cd"`



### 特殊情况case2

```java
public static final String A; // 常量A
public static final String B;    // 常量B
static {   
     A = "ab";   
     B = "cd";   
 }   
public static void main(String[] args) {
     String s = A + B;  
     String t = "abcd";   
    if (s == t) {   
         System.out.println("true");   
     } else {   
         System.out.println("false");   
     }   
} 

// false
```

A和B虽然被定义为常量，但是它们都没有马上被赋值。在运算出s的值之前，他们何时被赋值，以及被赋予什么样的值，都是不能确定的。因此A和B在被赋值之前，性质类似于一个变量。那么s就不能在编译期被确定，而只能在运行时被创建了。



## 转义符

`\\`  会转义成反斜杠 `\` ，而 `\\|` 的含义就是使用反斜杠对 | 进行转义。

```java
String t = "a||b||c||d";
String[] temp = t.split("\\|\\|");
System.out.println(temp.length);
```





# StringBuilder☕

## 概述

由于String类的对象内容不可改变，所以每当进行字符串拼接时，总是会在内存中创建一个新的对象，如果对字符串进行拼接操作，每次拼接，都会构建一个新的String对象，既耗时，又浪费空间，为了解决这一问题，可以使用`java.lang.StringBuilder`类。

- `StringBuilder `和 `StringBuffer ` 都是可变字符序列

- `StringBuilder `线程不安全，效率高；
- `StringBuffer` 线程安全，效率低。

`StringBuilder` 的内部拥有一个数组用来存放字符串内容，进行字符串拼接时，直接在数组中加入新内容，`StringBuilder` 会自动维护数组的扩容。

```java
StringBuilder sb = new StringBuilder("abc");a
System.out.println(Integer.toHexString(sb.hashCode()));  // 1540e19d
sb.setCharAt(0,'A');  // Abc
System.out.println(Integer.toHexString(sb.hashCode()));  // 1540e19d
```

可见，使用了方法前后的 `StringBuilder` 仍然是同一个对象，该对象在堆中的地址不变。



## 构造方法

- `public StringBuilder()`：构造一个空的StringBuilder容器。
- `public StringBuilder(String str)`：构造一个StringBuilder容器，并将字符串添加进去



## 常用方法

```java
// 生成StringBuilder对象
StringBuilder sb = new StringBuilder("abc");

// 添加方法
sb.append("efgh");  // abcefgh  append之后还是同一个对象

// 逆序方法
sb.reverse();       // hgfecba

// 插入方法
sb.insert(0, 'S').insert(0,'B');  // 链式调用，核心是该方法return this，将自身对象返回

// 删除方法
sb.delete(0,2).delete(0,2);  

// toString方法
sb.toString();  // 转化为不可变字符串对象
```



# Arrays☕

## 概念

`java.util.Arrays` 此类包含用来操作数组的各种方法，比如排序和搜索等，**其所有方法均为静态方法**



## 静态方法

### toString

返回指定数组内容的字符串表示形式

### sort

对指定的数组按数字升序进行排序。



### asList

调用 `Arrays.asList()` 创建 `List` 后， 调用 `add()`、`remove()` 方法会有 `java.lang.UnsupportedOperationException` ，原因是该方法的返回值是 `Arrays` 的内部类 `ArrayList` ，而不是 `java.util.ArrayList` 。

`Arrays` 的内部类 `ArrayList` 和 `java.util.ArrayList` 都是继承 `AbstractList`，`remove()`、`add()` 等方法 `AbstractList`中是默认`throw UnsupportedOperationException `而且不作任何操作。`java.util.ArrayList`重写了这些方法而 `Arrays` 的内部类`ArrayList` 没有重写，所以会抛出异常。

`Arrays.asList` 体现的是适配器模式，只是转换接口，后台的数据仍是数组。

可以转为 `java.util.ArrayList`：

```java
public class ListTest {
    public static void main(String[] args) {
        String[] strs = {"1","2","3","4","5"};
        List<String> tmp = Arrays.asList(strs);
        List list = new ArrayList(tmp);
        list.add("6");
    }
}
```



### hashCode



# Math☕

## 概述

`java.lang.Math` 类包含用于执行基本数学运算的方法，如初等指数、对数、平方根和三角函数。类似这样的工具类，**其所有方法均为静态方法**。



## 静态方法

```java
double d1 = Math.abs(‐5);   // 5
```



# Random☕

## 概述

`java.util.Random` 中类的实例用于生成伪随机数，以下代码能够得到一个随机数：

```java
Random r = new Random();
int i = r.nextInt();
```



## 构造方法

- `public Random()` ：创建一个新的随机数生成器。



## 成员方法	

```java
Random r = new Random();
r.nextInt();        // 随机生成一个int类型数据
r.nextInt(20);        // 随机生成一个范围是[0,20)的int类型数据
r.nextFloat();      // 随机生成一个float类型数据
r.nextBoolean();    // 随机生成一个boolean类型数据
```



## 随机数扩展

假设我们拥有一个随机数发生器rand7均匀产生 [1, 7] 的随机整数，如何构造 rand10 均匀产生 [1-10] 之间的整数

以下两种方法都需要配合「拒绝采样」策略。



### 二进制

1. 将 1-7 数字中的 7 移除掉，剩下 1-6 分别为一半奇数一半偶数，奇偶概率相同

2. 将 1-6 数据分别与 1 进行按位与操作，将 1-6 转换成 0 和 1

3. 4 次循环，每次由 1-6 数字产生 0-1 数字，生成 0-15 范围数字

   > 11 -> 1011 ，由 4 位二进制数表示

4. 由于 0-15 生成概率相同，丢弃掉 0 与 12 -15 保留 1 -11

   ```java
   /**
    * 生成[1,7]
    */
   public static int rand7() {
       return 1 + random.nextInt(8) % 7;
   }
   
   /**
    * 根据randon7的随机数产生0或1
    *
    * @return 0 or 1
    */
   private static int generateBit() {
       int i = 0;
       do {
           i = rand7();
       }
       while (i == 7);
       return i & 1;
   }
   
   /**
    *
    * @return [1, 11]
    */
   public static int rand11() {
       int x;
       do {
           x = 0;
           // 11 -> 1011 ，一共四位
           // 四次循环生成 0000 ~ 1111 之间的数
           for (int i = 0; i < 4; i++) {
               x = x << 1 | generateBit();
           }
       }
       // [1, 11]之外的丢弃，重新采样
       while (x < 1 || x > 11);
       return x;
   }
   ```

   





### 十进制

```java
public static int rand7() {
    // [1, 7]
    return 1 + random.nextInt(8);
}

public static int rand11() {
    int val = 0;
    do {
        val = 7 * (rand7() - 1) + rand7();
    }
    while (val > 11);
    return val;
}
```





# Scanner☕

## 概述

`java.util.Scanner` 是一个可以解析基本类型和字符串的简单文本扫描器。以下代码使用户能够从 System.in 中读取一个数。

```java
Scanner sc = new Scanner(System.in);
int i = sc.nextInt();
```

> System.in 系统输入指的是通过键盘录入数据。

## 方法

### 构造方法

- `public Scanner(InputStream source)` : 构造一个新的Scanner

### 成员方法

- `public int nextInt()` ：将输入信息的下一个标记扫描为一个 int 值。



## OJ输入



# Date☕

## 概述

` java.util.Date`类 表示特定的瞬间，精确到毫秒。

继续查阅Date类的描述，发现Date拥有多个构造函数，只是部分已经过时，但是其中有未过时的构造函数可以把毫秒值转成日期对象。

## 方法

`1970年1月1日 00:00:00` 为基准时间，每个度量单位是 `毫秒`

### 构造方法

- `public Date()`：分配Date对象并初始化此对象，以表示分配它的时间。
- `public Date(long date)`：传递毫秒值，分配Date对象并初始化此对象

> 东八区的基准时间为1970年1月1日8时0分0秒。

```java
Date d1 = new Date();       // Sun Aug 30 18:43:16 GMT+08:00 2020
Date d2 = new Date(0L);	    // Thu Jan 01 08:00:00 GMT+08:00 1970
Date d2 = new Date(2000L);  // Thu Jan 01 08:00:02 GMT+08:00 1970
d.getTime()    // 获取时间戳 1598784249508
d1.after(d2)   // 判断是否在某一时间之后  true   
d1.before(d2)  // 判断是否在某一时间之前  false
```



# DateFormat☕

## 概述

`java.text.DateFormat` 是日期/时间格式化子类的抽象类，我们通过这个类可以帮我们完成日期和文本之间的转换,也就是可以在Date对象与String对象之间进行来回转换。

* 格式化：按照指定的格式，从Date对象转换为String对象。
* 解析：按照指定的格式，从String对象转换为Date对象。

## 方法

### 构造方法

DateFormat是一个抽象类，一般使用它的的子类SimpleDateFormat类来实现。

* `public SimpleDateFormat(String pattern)`：用给定的模式和默认语言环境的日期格式符号构造SimpleDateFormat。

参数pattern是一个字符串，代表日期时间的自定义格式。

### 成员方法

DateFormat类的常用方法有：

- `public String format(Date date)`：将Date对象格式化为字符串。
- `public Date parse(String source)`：将字符串解析为Date对象。

```java
// 时间对象转格式化字符串
DateFormat df = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss");
df.format(new Date());  // 字符串 --> 2020-08-30 06:52:27
    
// 格式化字符串转时间对象
String date = new String("2020-09-04 10:10:10");
Date thetime = df.parse(date);  // 事件对象 ---> Fri Sep 04 00:34:56 GMT+08:00 2020
```

## pattern模式

常用的格式规则为：

| 标识字母（区分大小写） | 含义 |
| ---------------------- | ---- |
| y                      | 年   |
| M                      | 月   |
| d                      | 日   |
| H                      | 时   |
| m                      | 分   |
| s                      | 秒   |



# Calendar☕

## 概念

`java.util.Calendar` 是日历类，在Date后出现，替换掉了许多Date的方法。该类将所有可能用到的时间信息封装为静态成员变量，方便获取。日历类就是方便获取各个时间属性的。



## 方法

GregorianCalendar是Calendar的一个具体子类，提供了世界上大多数国家/地区使用的标准日历系统。

```java
// 生成日期对象

Calendar cal = new GregorianCalendar(2020, 7, 30, 18, 55, 30);
Calendar cal = Calendar.getInstance();  // getInstance返回Calendar的子类实例对象

// 获取日期
cal.get(Calendar.YEAR);
cal.get(Calendar.MONTH);
cal.get(Calendar.DATE);

// 设置日期
cal.set(<field>, <value>]);
cal.set(Calendar.YEAR, 2010);

// 日期的计算
cal.add(<field>, <value>]);
cal.add(Calendar.DATE, 5); 

// 日期对象和时间对象的转换
Date date = cal.getTime();  // 获取对应Date对象
cal4.setTime(date)  		// 通过date设定对应Calendar
```

## 日历字段

Calendar类中提供很多成员常量，代表给定的日历字段：

| 字段值       | 含义                                  |
| ------------ | ------------------------------------- |
| YEAR         | 年                                    |
| MONTH        | 月（从0开始，可以+1使用）             |
| DAY_OF_MONTH | 月中的天（几号）                      |
| HOUR         | 时（12小时制）                        |
| HOUR_OF_DAY  | 时（24小时制）                        |
| MINUTE       | 分                                    |
| SECOND       | 秒                                    |
| DAY_OF_WEEK  | 周中的天（周几，周日为1，可以-1使用） |



# System☕

## 概念

`java.lang.System` 类中提供了大量的静态方法，可以获取与系统相关的信息或系统级操作



## 方法

在System类的API文档中，常用的方法有：

- `public static long currentTimeMillis()`：返回以毫秒为单位的当前时间。
- `public static void arraycopy(Object src, int srcPos, Object dest, int destPos, int length)`：将数组中指定的数据拷贝到另一个数组中。

```java
public class demo {
    public static void main(String[] args) {
        long t1 = System.currentTimeMillis();
        for (int i = 0; i < 10000; i++) {
            System.out.println(i);
        }
        long t2 = System.currentTimeMillis();
        System.out.println((t2 - t1) + "毫秒");  // 57毫秒
    }
}
```

```java
public class demo {
    public static void main(String[] args) {
        int[] arr1 = {1, 2, 3, 4, 5};  // [1, 2, 3, 4, 5]
        int[] arr2 = new int[5];	   // [0, 0, 0, 0, 0]
        System.arraycopy(arr1, 1, arr2, 1, 3);  // [0, 2, 3, 4, 0]
    }
}
```





## System.out

`System.out` 是 `System` 静态数据成员，这个成员是 `java.io.PrintStream` 类的引用，JVM 在启动时会对这个变量进行初始化。

`println()` 就是 `java.io.PrintStream` 类里的一个方法，它的作用是向控制台输出信息。

因为 `System.out` 是 `java.io.PrintStream` 类的实例的引用，所以可以通过 `System.out.println()` 来调用此方法。



# Unsafe☕

## 概念

Java和 C++ 的一个重要区别就是 Java 无法直接操作一块内存区域，不能像 C++ 那样可以自己申请内存和释放内存。而  `Unsafe` 类提供了类似 C++ 手动管理内存的能力。

*JDK 5* 之后，Java 类库中才开始使用 CAS 操作，该操作由 `sun.misc.Unsafe` 类里面的 `compareAndSwapInt()` 和 `compareAndSwapLong()` 之类的几个方法包装提供。





## getXXX

普通读写

```java
public native int getInt(Object o, long offset);

public native void putInt(Object o, long offset, int x);
```





## getXXXVolatile

volatile读写

```java
public native int getIntVolatile(Object o, long offset);

public native void putIntVolatile(Object o, long offset, int x);
```





## compareAndSwapXXX

compareAndSwapXXX 相关的方法就是借助 C 语言来调用 CPU 底层指令实现的

参数 o: 表示要操作的对象

参数 offset: 表示要操作象中属性地址的偏移量

参数 expected: 表示需要修改数据的期望的值

参数 x: 表示需要修改为的新值

```java
public final native boolean compareAndSwapInt(Object o, long offset,
                                              int expected,
                                              int x);
```



### getAndAdd/SetXXX

该方法同时只有一个线程能够执行，其他线程会进入忙自旋。

同一时刻只有一个线程能够完成 CAS 操作，其他线程执行到 A 的时候，v 已经变成了旧值，CAS 会返回 false，然后 dowhile 循环会一直执行。并发程度越高该方法的性能也就越低。

```java
public final int getAndAddInt(Object o, long offset, int delta) {
    int v;
    do {
        v = getIntVolatile(o, offset);
    } while (!compareAndSwapInt(o, offset, v, v + delta));  // A
    return v;
}

public final int getAndSetInt(Object o, long offset, int newValue) {
    int v;
    do {
        v = getIntVolatile(o, offset);
    } while (!compareAndSwapInt(o, offset, v, newValue));
    return v;
}
```







# Guava☕

https://blog.csdn.net/github_shequ/article/details/112001280

